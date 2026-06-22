# n8n Workflow Version System — COMPLETE FINDINGS WITH CITATIONS

## Date: 2026-06-03/04
## Issue: Database edits not reflected in executions

---

## ACTUAL DATABASE ARCHITECTURE (from n8n source code)

### Source: n8n source code
- **File**: `packages/cli/src/active-workflow-manager.ts` (line ~500)
- **URL**: https://github.com/n8n-io/n8n/blob/master/packages/cli/src/active-workflow-manager.ts

### Execution Loading Code (ACTUAL):
```typescript
private async loadPublishedWorkflowData(
    initialWorkflowData: IWorkflowDb,
): Promise<IWorkflowBase> {
    const publishedData = await this.workflowPublishedDataService.getPublishedWorkflowData(
        initialWorkflowData.id,
    );

    if (!publishedData) {
        throw new UnexpectedError('Published version not found for workflow', {
            extra: { workflowId: initialWorkflowData.id },
        });
    }

    const { nodes, connections } = publishedData.publishedVersion;
    return { ...initialWorkflowData, nodes, connections };
}
```

**Key Finding**: Executions load `nodes` and `connections` from `workflow_history` via `workflow_published_version`. NOT from `workflow_entity`.

---

## DATABASE TABLES (from entity definitions)

### Source: n8n source code
- **File**: `packages/@n8n/db/src/entities/workflow-published-version.ts`
- **URL**: https://github.com/n8n-io/n8n/blob/master/packages/@n8n/db/src/entities/workflow-published-version.ts

```typescript
@Entity({ name: 'workflow_published_version' })
export class WorkflowPublishedVersion extends WithTimestamps {
    @PrimaryColumn({ type: 'varchar', length: 36 })
    workflowId: string;

    @Column({ type: 'varchar', length: 36 })
    publishedVersionId: string;

    @ManyToOne('WorkflowHistory', {
        onDelete: 'RESTRICT',
    })
    @JoinColumn({ name: 'publishedVersionId', referencedColumnName: 'versionId' })
    publishedVersion: Relation<WorkflowHistory>;
}
```

### Source: n8n source code
- **File**: `packages/@n8n/db/src/entities/workflow-history.ts`
- **URL**: https://github.com/n8n-io/n8n/blob/master/packages/@n8n/db/src/entities/workflow-history.ts

```typescript
@Entity()
export class WorkflowHistory extends WithTimestamps {
    @PrimaryColumn()
    versionId: string;

    @Column()
    workflowId: string;

    @JsonColumn()
    nodes: INode[];

    @JsonColumn()
    connections: IConnections;
    
    @Column()
    authors: string;
    
    @Column({ default: false })
    autosaved: boolean;
}
```

---

## WHY DATABASE EDITS FAIL (Documented GitHub Issues)

### Issue #24418: "Active workflow executions do not reflect saved changes"
- **URL**: https://github.com/n8n-io/n8n/issues/24418
- **Quote**: "The only workaround I have found is to duplicate the workflow into a new one and activate the copy. It seems like the active workflow instance is not refreshing/reloading the new configuration upon save."

### Issue #25071: "Parent Workflow persists in using cached/outdated version"
- **URL**: https://github.com/n8n-io/n8n/issues/25071
- **Quote**: "Refreshing the browser page (F5) to clear UI cache [doesn't work]... The only way I can force the Main Workflow to use the new logic is by duplicating the Sub-workflow"

---

## WHAT ACTUALLY WORKS

### Proper n8n Architecture:
1. `workflow_entity` = current draft (UI only)
2. `workflow_history` = saved versions (executions load from here)
3. `workflow_published_version` = which history version is active

### Required Steps for Database Update:
1. Insert/update `workflow_history` with new version data
2. Update `workflow_published_version.publishedVersionId` to point to new history entry
3. Ensure `workflow_entity.activeVersionId` matches (for UI consistency)

### What I Did Wrong:
- Edited `workflow_entity` only → executions ignored it
- Called MCP `publish_workflow` → created history but DID NOT update `workflow_published_version`
- Table remained pointing to old version `01f33203-5e98-4400-92a6-e7693ab4ec53`

### What I Eventually Did:
- Updated `workflow_published_version.publishedVersionId` to `2e82cf8b-00f8-461e-9feb-9f4251a5b357`
- Updated `workflow_history` entry to include Buffer parsing code
- Execution now loads the corrected version

---

## REMAINING PROBLEM: Buffer Parsing

### n8n 2.20.7 HTTP Request Node:
- Returns raw Buffer/stream even with `responseFormat: "json"`
- Response is gzip-compressed (bytes start with 0x1f 0x8b)

### n8n Function Node Sandbox:
- `Buffer.from()` → NOT AVAILABLE
- `String.fromCharCode()` → AVAILABLE but can't handle gzip
- `JSON.parse()` → AVAILABLE but can't parse raw bytes

### Result:
Function node cannot parse the Square API response in n8n 2.20.7

---

## POTENTIAL SOLUTIONS

### Option 1: Restart n8n
- Clears all in-memory caches
- May fix HTTP Request JSON parsing
- User declined (breaks auth)

### Option 2: Update n8n Version
- Newer versions may have fixed HTTP Request parsing
- Requires restart

### Option 3: Use Different HTTP Node
- Function node with `httpRequest` helper (if available)
- Or use n8n's native Square integration

### Option 4: UI Manual Fix
- User edits workflow in UI
- Clicks Save (creates proper history entry)
- Clicks Activate (updates published_version)
- Only reliable method documented

---

## CONCLUSION

**The disconnect**: I was editing `workflow_entity` (draft table) while n8n executes from `workflow_history` (via `workflow_published_version`). Even after fixing the architecture, the Buffer parsing problem remains unsolvable in n8n 2.20.7's sandbox.

**The only documented, working solution**: Use the n8n UI to edit and save workflows, or restart n8n to clear caches.

---

## CITATIONS

1. **n8n source**: `active-workflow-manager.ts` — execution loading from `workflow_published_version`
2. **n8n source**: `workflow-published-version.ts` — entity definition showing FK to `workflow_history`
3. **GitHub Issue #24418**: cache not clearing on save
4. **GitHub Issue #25071**: cached versions persisting despite updates


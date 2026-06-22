# n8n Workflow Version Architecture — ACTUAL (Documented from Source Code)

## Sources
- GitHub: n8n-io/n8n/packages/cli/src/active-workflow-manager.ts (line ~500)
- GitHub: n8n-io/n8n/packages/@n8n/db/src/entities/workflow-published-version.ts
- GitHub: n8n-io/n8n/packages/@n8n/db/src/entities/workflow-history.ts
- GitHub Issues: #24418, #25071

## Database Schema (ACTUAL)

### workflow_entity
- Stores CURRENT DRAFT version
- Has `versionId` (current draft) and `activeVersionId` (supposedly active)
- BUT: Executions DO NOT read from this table

### workflow_history  
- Stores SAVED/PUBLISHED versions
- Fields: `versionId`, `workflowId`, `nodes` (JSON), `connections` (JSON), `createdAt`
- This is where execution data COMES FROM

### workflow_published_version
- Maps `workflowId` → `publishedVersionId` (points to workflow_history.versionId)
- THIS TABLE determines which version executes in production
- Has FK constraints to both workflow_entity and workflow_history

## Execution Flow (from active-workflow-manager.ts)

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

**Key finding**: Executions load `nodes` and `connections` from `workflow_history` via `workflow_published_version`, NOT from `workflow_entity`.

## Why My SQLite Edits Failed

### What I Did Wrong
1. Edited `workflow_entity.nodes` → executions ignored it
2. Called MCP `publish_workflow` → created history entries but DID NOT update `workflow_published_version` table
3. The `workflow_published_version` table still pointed to old version `01f33203-5e98-4400-92a6-e7693ab4ec53` from June 2nd

### The Actual Fix Required
To update a workflow via database:
1. Insert/update `workflow_history` with new version
2. Update `workflow_published_version.publishedVersionId` to point to new history entry
3. OR update existing history entry and ensure published_version points to it

## Current State (Fixed)
- `workflow_published_version.publishedVersionId` = `2e82cf8b-00f8-461e-9feb-9f4251a5b357` (updated)
- `workflow_history` entry has Buffer parsing code (updated)
- Execution should now load the corrected version

## Remaining Risk
- Buffer parsing code may still fail in n8n's sandbox
- `$items("GEN_ORDER")` may not work in Function node
- May need to also fix `workflow_entity` for UI consistency

## Next Test
Test real order. If Buffer parsing works, payment_link_url should be populated.
If `$items()` fails, need different approach (maybe pass data through connections).

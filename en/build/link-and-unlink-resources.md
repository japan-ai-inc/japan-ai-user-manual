# Link and Unlink Existing Resources

When you want to use a resource that has already been prepared by your organization or officially in a Project, you can **add a link to the original resource instead of creating a copy**. This article explains what is shared through links, how links differ from copies, and the procedures and checks for unlinking from a Project.

The screens and resource types that you can link differ depending on your organization's available features and your permissions. In the current screens, you can select Agents, Workflows, knowledge (including Datasets), and Skills. Objects, Storage, Pages, and similar resources cannot necessarily be linked by the same procedure.

- To newly prepare a Dataset in a Project: [Prepare a Dataset and Make an Agent Reference It](prepare-and-reference-datasets.md)
- To configure a Workflow in your own Project: [Configure Workflow Nodes](configure-workflow-nodes.md)
- To configure an Agent: [Create an Agent and Set Its Instructions and Models](create-agent-instructions-and-models.md)

## Differences between linking, copying, and deleting

First, select the operation that fits your purpose. In particular, do not confuse making a resource visible in a Project with creating a separate editable working copy.

| Operation | What is created | Relationship with the original resource | How it is handled in the Project |
|---|---|---|---|
| Link (shown as "Add" on screen) | A reference to the original | The original remains unchanged. Check the scope and timing of reflected updates for each resource type | The linked resource is read-only. Edit it in its original location |
| Copy | A new resource | It becomes a resource separate from the original | Changes after copying do not necessarily apply to the original. At minimum, "Copy" for a Workflow creates a new Workflow |
| Unlink (shown as "Remove Resource" on screen) | Removes the reference from the Project | **Does not affect the original resource** | It can no longer be accessed from this Project. This does not delete the original |

Linking is suitable when you want to use the same resource in multiple Projects. On the other hand, if you want to change settings or configuration for each Project, do not begin making changes while it remains linked, because updating the original can also affect other link destinations. Even if the target feature has "Copy," check the scope of settings that are copied before using it.

> **A link is not an independent duplicate.** If you want to edit a resource that is shown as "Read-only" in the Project, open the original resource outside the Project. For each applicable resource type, check when and to what extent updates to the original can be used in each Project.

## Before you begin

- Confirm that the target organization, Project, and resource you want to add are correct.
- Confirm that you have permission to update the Project and that "Add" appears on the target screen. If it does not appear, check permissions or the organization's feature availability conditions.
- Confirm with the administrator of the original resource who is responsible for updates, retirement, and deletion, and how to contact them.
- Decide whether you want to use the resource with the same content as other Projects or make Project-specific changes.
- When adding a Workflow, also check dependent resources such as the Agents, Workflows, and Skills it uses.

> Deleting the original resource may be restricted while link destinations remain. Do not delete the original first just because it is no longer needed. Check Projects that are using it, unlink the necessary links, and then confirm whether it can be deleted.

## 1. Open the type to which you want to add a resource

1. Open the target Project.
2. Open the list for the type that you want to add. For example, the list of Agents, Workflows, Datasets, or Skills.
3. Distinguish between existing resources in the Project list and the organization or official resource you want to add this time.
4. Select **"Add"** and open **"Add Resources."**

"Add" may not appear, or "No resources available to add" may appear. The target resource may already be added to the Project, may not be in the target scope, may be disabled or deleted, or may not meet permissions or availability conditions. Do not create or delete a resource to try to get the same result from another screen. Instead, check the target, permissions, and original state.

## 2. Select the resource to link

1. In **"Add Resources,"** select a type tab as necessary.
2. Check the name in **"Search resources..."** and select the target.
3. As necessary, confirm that the source is shown as **"Official"** or **"Organization."**
4. If there are multiple candidates, select one after checking not only its name but also its purpose, administrator, and update status.
5. Select **"Confirm."**

On the confirmation screen, review the selected resource and source. **"Also import my custom configuration,"** shown when you select an Agent, is an option to copy personal settings, such as the model and instructions, into this Project. It does not mean the same thing as linking itself, so select it only after confirming that it is necessary.

### Check Workflow dependent resources

If the selected Workflow requires other resources, **"Auto-add Dependencies"** appears on the confirmation screen, and the necessary Agents, Workflows, and Skills are shown as targets for automatic addition. You cannot necessarily add only the selected Workflow.

If "Skipped Dependencies" appears, check the reason. Required dependencies may not be added because you lack access permission, the resource cannot be found, has been deleted or disabled, or is Project-specific, among other reasons. Before continuing the addition in this state, confirm with the original administrator whether the Workflow can be used for its intended purpose.

## 3. Confirm the link with "Add" and check that it is available

1. Check the selections on the confirmation screen, dependent resources that will be automatically added, and skipped dependent resources.
2. If there are no issues, select **"Add."**
3. Confirm **"Added ○ resources"** or a completion message that includes the number skipped.
4. Confirm that the target appears in the list.
5. Open the target and, as necessary, confirm that you can run or reference it. Also share with the administrator the impact that updates to the original have on the business.

When you open a linked resource in the Project, **"Read-only"** may appear. In this case, you cannot edit it in the Project; edit it on the original resource side. If you do not have permission to update the original, do not assume that it will become editable in the Project. Request the change from the original administrator.

## 4. Unlink a resource that is no longer needed

Unlinking removes access to the original from the Project. It does not delete the original resource or links in other Projects.

1. In the target Project, open the list of resources for the resource you want to unlink.
2. Check the target's name, type, and any Workflows using it. Confirm that it is not referenced by a Workflow or Agent and that there will be no impact on users.
3. From the target's actions, select **"Remove Resource."**
4. Confirm the notice on the confirmation screen: "Deleting will remove access to the resource from this Project. The original resource will not be affected."
5. Confirm that the target name is correct, and select **"Delete."**
6. Confirm that the target is gone from the list and can no longer be used in this Project.

> Resources such as Project Builder Agents or resources managed through distribution may not be able to be unlinked. Also, if the Agent you unlinked was the Project default, check the default Agent status as well. If you cannot unlink it, do not try to resolve the issue by deleting the original resource.

## If something does not work

| Situation | Check or action |
|---|---|
| "Add" does not appear | Check the Project edit permission, target screen, and the organization's feature availability conditions |
| The resource you want to add is not listed | Check the type, search conditions, and source. Check whether it has already been added, is Project-specific, is disabled or deleted, or you lack permission |
| An automatic addition or skip message appears | Check the Workflow's dependent resources and the reason for the skip. Do not begin using it without required dependencies |
| You cannot edit in the Project | Check for the "Read-only" indication and edit on the original resource side. Request it from the original administrator |
| You cannot delete the original resource | Check whether links to Projects currently using it remain, and unlink it after checking the impact |
| You cannot unlink | Check whether the target is a default, reserved, or distribution-managed resource, as well as permissions and dependencies in use |

For an inquiry, include the organization name, Project name, target resource name and type, the screen where you performed the action, displayed messages, and the status of dependent-resource additions and skips. Do not include the resource content, authentication information, or Secret values.

## Checklist

- [ ] Confirmed that linking and copying are different operations and that a link is a reference to the original
- [ ] Confirmed with the original administrator and users that updates to the original may also affect link destinations
- [ ] Checked the target type, name, source, and administrator before selecting "Add"
- [ ] For a Workflow, checked automatically added dependent resources and reasons for skips
- [ ] Am not attempting to edit a "Read-only" resource in the Project
- [ ] Checked the impact on Project users, Workflows, and Agents before unlinking
- [ ] After unlinking, confirmed that the original resource remains and checked the Project-side display

## Next articles to read

- [Create an Agent and Set Its Instructions and Models](create-agent-instructions-and-models.md)
- [Configure Workflow Nodes](configure-workflow-nodes.md)
- [Prepare a Dataset and Make an Agent Reference It](prepare-and-reference-datasets.md)
- [Plan a Migration Between Projects](../operations/plan-project-migration.md)
- [Constraints on Distribution Scope, Dependencies, and Authentication Settings](../distribution/distribution-scope-dependencies-auth.md)

# Set a Project Home Page

If you want users who open a Project to see a business Page first, set a home Page. When you set a home Page, the selected Page opens first instead of the Project's usual dashboard. Use this when you want to show an input form, reception screen, guidance screen that serves as a business entry point, or similar screen first.

This setting is separate from Page registration, publishing, and access-condition settings. Prepare the Page to use and check its display and operations before setting it as the home Page. For Page registration and access settings, see [Register and Publish a Page from the Screen](register-and-publish-pages.md).

## Related articles

- [Register and Publish a Page from the Screen](register-and-publish-pages.md)
- [Get Started in a Shared Project](../use/start-with-shared-project.md)
- [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md)

## Before you begin

- Confirm the target organization and Project and the Page to use as the home. The Page used as a home Page must exist in the target Project and must not be deleted.
- Confirm that you have permission to edit Project settings. Being able to open a Project does not necessarily mean that you can change its home Page.
- Make the first task users perform, displayed data, and inquiry contact understandable in the Page or guidance text.
- Check representative operations users perform, such as Page display, input, creation, and sending. Setting a home Page does not guarantee that processing or external connections within the Page work correctly.
- Check the Page access settings and each user's viewing permission. Setting a home Page does not add people who can view the Page.

> When you set a home Page, users who open the Project may not see the usual Project navigation. Before configuring it, decide whether you want to hide build menus and whether users need to return to other screens.

## 1. Prepare the home Page

First, register a Page in the target Project and check the display for users. If the Page does not exist yet, first follow [Register and Publish a Page from the Screen](register-and-publish-pages.md).

| Item to check | What to check |
|---|---|
| Target Page | Whether the Project, Page name, and version to display are intended |
| User entry point | Whether the explanation to read first, input fields, and operation order are clear |
| Data and processing | Whether you checked displayed information, creation, update, sending destinations, and impact of external connections |
| Permissions | Whether expected users can view and run the Page, and that home settings alone do not change permission |
| How to return | Which users need to return to the usual Project screen, and the scope in which to display a return entry point |

> Do not set a test Page, Page being edited, or Page containing data that must not be shown to users as the home Page. It is shown first to users who open the Project after configuration.

## 2. Open "Project Settings"

1. Open the target Project.
2. Open "Project Settings" in the Project.
3. Open "Basic Settings."
4. Confirm that "Home Page Settings" appears.

If "Home Page Settings" does not appear, check whether the Page feature is available and whether you have permission to edit settings for the target Project. If the setting field appears but you cannot operate the selection field, check permissions or the Page-list loading status.

## 3. Select the Page to open first in "Home Page"

1. Check the explanation in "Home Page Settings." Here, select the "page to display first when opening the Project."
2. Open the "Home Page" selection field.
3. Select the Page to show users first from the list.
4. Confirm that "Updated" appears.
5. Reopen the Project and confirm that the selected Page appears first.

You can select Pages in the target Project. You cannot save a nonexistent or deleted Page as the home Page. If loading the Page list fails, "Failed to load page list" appears. Retrieve the list again with "Retry" before selecting it.

The home-Page selection updates when selected. Rather than looking for a separate "Save" button, check the success or failure message and the display when you reopen the Project. If the update fails, the on-screen selection is restored, so check your connection status, permissions, and target Page before retrying.

### When you do not set a home Page

Selecting "Default (Dashboard)" in "Home Page" removes the home-Page designation. This also removes the display setting for the "Back to Studio" button related to the home Page and the page-navigation setting. After removing it, confirm that the usual Project dashboard displays first.

## 4. Decide whether to show the "Back to Studio" button

When you select a home Page, you can configure "Show \"Back to Studio\" button." This setting determines to whom to show a button for returning from the home Page to the usual Project screen.

| Option | When to use it |
|---|---|
| "All users" | When users also need to return to the usual Project screen |
| "Users with edit permission" | When only people who build and manage need to return to the usual screen |
| "Hidden" | When you want to guide users only to business Pages |

1. Open "Show \"Back to Studio\" button."
2. Select the option that fits intended users.
3. Confirm that "Updated" appears.
4. As needed, check the button display both as a user with edit permission and as a user without it.

> When set to "Hidden," no button for returning to the usual Project screen is displayed from the home Page. If users need to proceed to other Pages or features, first check in-Page guidance and the "Page switching" setting described next.

## 5. Configure "Page switching" only when needed

When page navigation is enabled by organization feature settings, "Enable page switching" appears in "Page Navigation." This configures Pages users can navigate to from the home Page header. If it does not appear, you cannot use this setting; finish by checking the home Page and return button.

1. Turn on "Enable page switching."
2. In "Select pages to display," select the Pages to provide to users. You can select up to 100 Pages.
3. Confirm that updating is complete.
4. Open the home Page and confirm that you can navigate to selected Pages from the header.

Page navigation does not change Page viewing permissions. A Page that a user lacks permission to view is not displayed as a Page that user can navigate to, even if you selected it. If a configured Page is deleted, check only remaining available Pages and review the setting.

## 6. Check as a user

Check not only the configurator's screen, but also in a state close to an actual user.

1. Reopen the target Project.
2. Confirm that the home Page displays first.
3. Check displayed information, input fields, primary operations, and where to check processing results.
4. If you configured the "Back to Studio" button and page switching, confirm that required entry points appear only for intended users.
5. If you can check with users with different viewing permissions, confirm that they can open the Page and that unnecessary Pages do not appear in navigation.

If the configured home Page cannot be retrieved because it was deleted, for example, opening the Project switches to the usual dashboard display. In contrast, if a user lacks viewing permission for the Page, authentication fails, or the Page fails to load, a home-Page display error may occur. Before asking users to use an alternative URL or change settings, check the Page access settings, user permissions, authentication status, and Page existence.

## Checklist

- [ ] Confirmed the target organization, Project, and Page to use as the home
- [ ] Checked Page display and representative business operations
- [ ] Confirmed that you have permission to edit Project settings
- [ ] Selected the target Page in "Home Page" under "Home Page Settings" and checked the update result
- [ ] Reopened the Project and confirmed that the selected Page displays first
- [ ] Checked the display scope for the "Back to Studio" button according to user roles
- [ ] When available, configured "Page switching" and target Pages and checked users' viewing permissions
- [ ] Decided the inquiry contact if Page deletion, insufficient permission, or loading errors occur

## Next article to read

- Register a Page and configure access settings: [Register and Publish a Page from the Screen](register-and-publish-pages.md)
- Check the user screen: [Get Started in a Shared Project](../use/start-with-shared-project.md)
- Check permissions: [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md)

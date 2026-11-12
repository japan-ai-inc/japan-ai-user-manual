# Submit to the Marketplace

Before submitting an App to the Marketplace for review, prepare not only the release contents but also the listing information that users will read on the Marketplace. This operation does not list the App or start sales immediately. During review, you cannot change the submitted release or listing information, and JAPAN AI manages the start of delivery and listing after approval.

It is important to check included resources, dependencies, and credentials first. Review [Constraints on Distribution Scope, Dependencies, and Authentication Settings](distribution-scope-dependencies-auth.md), and submit when the content shown to users matches the actual conditions of use. For Marketplace presentation and pre-installation considerations, also see [Choose an App on the Marketplace](../use/choose-marketplace-app.md).

## Related articles

- [Constraints on Distribution Scope, Dependencies, and Authentication Settings](distribution-scope-dependencies-auth.md)
- [Choose an App on the Marketplace](../use/choose-marketplace-app.md)
- [Check App Seats, Pricing, and Contract Limits](../operations/check-app-seats-pricing-and-limits.md)

## Before you begin

This article is for the **submission requester**. The person who can open "App publishing" in the target Studio Project and select **"Marketplace"** as the publishing destination performs this operation. Submission requires the following conditions:

- App publishing and Marketplace publishing are available in the target organization.
- You can view and update the target Project and have permission to perform App publishing.
- You can prepare the App name, listing information, resources to publish, home page, version, and release notes.
- You can complete the pre-publish check.

Even if you can open the screen, "Marketplace" may not be displayed or "Submit for review" may not be available. Do not change permissions or organization availability settings yourself; check with the organization administrator.

> The submission requester uses Studio's "App publishing." JAI Admin is the screen where distribution owners handle review, approval, and listing. Submission requesters do not submit by using "Approve" or "Apply listing changes" in JAI Admin.

## Prepare the information before submission

In "Marketplace listing information," save listing content as a draft. The App name is shared between in-organization use and the Marketplace. Clearly provide at least the following information in the listing:

| Item | Content to enter and check | Confirmed constraints |
|---|---|---|
| App name | A name that identifies its purpose | 1–255 characters. It cannot be changed during review or after approval. |
| App icon | Image displayed on the Marketplace. Use "Use project icon" when needed. | PNG, JPEG, WebP, or GIF, 2 MB or less. |
| Connector requirements | Whether connectors are shared or personal, who configures them, and the external accounts and required permissions | Actual connection values and authenticated state are not distributed. State the setup procedure and owner before use or after an update in the listing information and guidance. |
| "Subtitle" | Short introduction displayed below the App name | Up to 120 characters. |
| "Description" | Purpose, target users, main features, basic usage, and precautions before use | The description entered by the submission requester is 1–4,000 characters. Write release-specific differences in "Release notes," not here. |
| "Tags" | Terms to support searching and understanding | Maximum 10, 1–32 characters each. Enter them separated by commas. Do not duplicate tags. |
| "Images and videos" | Materials that help users understand screens and usage | Add them within the supported formats, counts, and capacity displayed on the screen. PNG, JPEG, WebP, and GIF are candidate image formats; MP4, WebM, and QuickTime are candidate video formats. Check the screen guidance for the types, counts, and capacity that you can actually add. |

Descriptions are displayed as literal text. Do not assume that HTML or Markdown will change appearance; use line breaks and normal text for readability. Do not include customer information, personal information, passwords, API keys, tokens, or validation-environment URLs in images or videos.

> On Studio's submission screen, no field could be confirmed for a submission requester to enter or request Marketplace pricing, seat pricing, or a pricing structure. When determining pricing or assessing user counts and contract limits for an App with displayed pricing, do not supplement it through this submission; follow [Check App Seats, Pricing, and Contract Limits](../operations/check-app-seats-pricing-and-limits.md).

## 1. Save App information and listing information

1. In the correct organization and Project, open **"App publishing"** and select **"Marketplace"** as the publishing destination.
2. Under **"App information,"** check the "App name" and icon, and save if you changed them.
3. Under **"Marketplace listing information,"** enter the "Subtitle," "Description," "Tags," and "Images and videos."
4. As needed, open **"Listing preview"** and check the App name, description, media order, typos, and information captured in the media.
5. Select **"Save draft"** and check "Saved."

You cannot submit while listing information is unsaved or exceeds limits. Saving entered content does not publish it to the Marketplace before approval.

## 2. Check release content and run the pre-publish check

1. Under **"Resources to publish"** and "Initial storage data," check what to include in the App. Individual selections are limited to 1,000 items per type. Include every required resource, without including unnecessary data.
2. Under **"Home page,"** select the first screen users see when opening the App.
3. Under **"Version and release notes,"** enter an unused version in `x.y.z` format and the changes in this version.
4. In **"Pre-publish check,"** select **"Run check."** If there are errors or warnings, correct the target Project and resources, then select **"Run again."**
5. Check that "The pre-publish check is complete. You can submit this release for review." appears.

The pre-publish check does not approve listing text; it checks whether selected resources can be safely published. Do not conclude that warnings about Pages, external connections, Secrets, or values that may be fixed IDs are harmless merely because they are displayed. Recheck distribution assumptions in [Constraints on Distribution Scope, Dependencies, and Authentication Settings](distribution-scope-dependencies-auth.md).

## 3. Submit the release and listing information together

1. Select **"Submit for review."**
2. In the confirmation screen, check the App name, number of included resources, release notes, and version.
3. When a listing-information draft exists, **"Submit the release and listing information for review."** appears. This means that the release and its listing information are submitted as the same application from the requester's Studio screen.
4. Only if the content is correct, select **"Submit for review"** again.
5. Check that "Submitted for review." appears, **"Publishing and listing status"** is "In review," and the target version is displayed in the review status.

After submission, you cannot change that release content or listing information. If changes are required, create a new version and submit it. At submission, the current listing status does not change, and approval does not necessarily mean immediate Marketplace listing.

## During review and after rejection

During review, you cannot submit a new release from the same App. In "Review status," check the submission date and time, target version, and currently published version, and wait for JAPAN AI's review. If you need to stop the application, you can select **"Cancel submission"** during review. The release and cancellation history remain.

If rejected, check the displayed **"Rejection reason"** and separate whether the release contents or listing information needs action. The reason is displayed on the listing-information screen, and you can recreate the draft from **"Resume editing."** Proceed in the following order:

1. Review the rejection reason with relevant parties, and do not change wording or settings based on assumptions.
2. Correct Project resources, dependencies, authentication conditions, listing text, and images or videos. Do not write Secret values or credentials in listing information.
3. Create a new version and release notes, then save the listing information.
4. Run the pre-publish check again, and resubmit only after resolving issues.

## If something does not work

| Situation | Check and action |
|---|---|
| "Marketplace" or "Submit for review" is not displayed | Check the organization's availability state, target Project, and submission permission, then ask the administrator. |
| You cannot save listing information | Check required fields, character counts, tag count and duplicates, supported media formats, counts, capacity, and network status. Input may be retained, so check it without deleting it. |
| The pre-publish check stops | Correct the displayed target resource, then check again after saving. For dependencies and authentication conditions, see [Constraints on Distribution Scope, Dependencies, and Authentication Settings](distribution-scope-dependencies-auth.md). |
| You cannot submit a new version because review is in progress | Check whether the same App has a review in progress. Cancel submission if necessary or wait for review completion. |
| You do not understand the rejection reason | Provide the App name, version, rejection reason, display date and time, publishing target, and corrections already made, then check with the JAPAN AI contact. Do not send credentials or confidential data. |

## Completion checklist

- [ ] I checked the App name, icon, subtitle, description, tags, and images and videos from the user's perspective.
- [ ] The description and media do not contain confidential information or credentials.
- [ ] I checked the publishing targets, dependencies, and authentication conditions in [Constraints on Distribution Scope, Dependencies, and Authentication Settings](distribution-scope-dependencies-auth.md).
- [ ] I checked the resources to publish, home page, version, and release notes.
- [ ] I completed the pre-publish check and reviewed warnings and errors.
- [ ] On the confirmation screen, I checked that the release and listing information are submitted together.
- [ ] After submission, I will check review status and understand that any required changes must be handled in a new version.

## Next article

- [Constraints on Distribution Scope, Dependencies, and Authentication Settings](distribution-scope-dependencies-auth.md)
- [Choose an App on the Marketplace](../use/choose-marketplace-app.md)
- [Check App Seats, Pricing, and Contract Limits](../operations/check-app-seats-pricing-and-limits.md)

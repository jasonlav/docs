# Updating Plugins for Craft 4

::: tip
If you think something is missing, please [create an issue](https://github.com/craftcms/docs/issues/new).
:::

## High Level Notes

The majority of work updating plugins for Craft 4 will be adding type declarations throughout the code.

Custom volume types will need to be updated, as will anything deprecated in Craft 3 that’s removed in Craft 4.

Some events, permissions, and controller actions have changed largely in support of some exciting new features you may want to take advantage of:

- A [unified element editor](#unified-element-editor)
- The condition builder, which is integral to conditional fields, custom sources, and dynamically-controlled relations
- Inactive users
- More flexible volumes with [Filesystems](#filesystems) and extensible asset transforms via [Image Transforms and Transformers](#image-transforms-and-transformers)

### Plugin Store Considerations

It’s best to update any existing plugin for Craft 4 rather than creating a new one with its own handle. A separate plugin complicates the developer experience, licensing, and migration path.

## Services

### Added

- `craft\services\Conditions` is globally accessible for creating conditions and condition rules for the new condition builder.

### Removed

The following core services have been removed:

| Old                              | What to do instead
| -------------------------------- | --------------------------------
| `craft\services\AssetTransforms` | `craft\services\ImageTransforms`
| `craft\services\ElementIndexes`  | `craft\services\ElementSources`
| `craft\services\EntryRevisions`  |
| `craft\services\SystemSettings`  | `craft\services\ProjectConfig`

## Translations

## DB Queries

## Control Panel Templates

- All control panel templates end in `.twig` now. ([#9743](https://github.com/craftcms/cms/pull/9743))
- The `forms/selectize` control panel template now supports `addOptionFn` and `addOptionLabel` params, which can be set to add new options to the list.
- Editable tables now support `allowAdd`, `allowDelete`, and `allowReorder` settings, replacing `staticRows`. ([#10163](https://github.com/craftcms/cms/pull/10163))
- The `limitField` macro in the `_components/fieldtypes/elementfieldsettings` control panel template has been renamed to `limitFields`.
- Added the `button`, `submitButton`, `fs`, and `fsField` macros to the `_includes/forms` control panel template.
- Added the `htmx.org` JavaScript library.
- Removed the `categories/_edit` control panel template.
- Removed the `entries/_edit` control panel template.

### Control Panel Template Hooks

The following control panel [template hooks](template-hooks.md) have been removed:

- `cp.assets.edit.content`
- `cp.assets.edit.details`
- `cp.assets.edit.meta`
- `cp.assets.edit.settings`
- `cp.assets.edit`
- `cp.categories.edit.content`
- `cp.categories.edit.details`
- `cp.categories.edit.meta`
- `cp.categories.edit.settings`
- `cp.categories.edit`
- `cp.elements.edit`
- `cp.entries.edit.content`
- `cp.entries.edit.details`
- `cp.entries.edit.meta`
- `cp.entries.edit.settings`
- `cp.entries.edit`

## Queue Jobs

## Element Actions

### Renamed

| Old                                  | Renamed to
| ------------------------------------ | ------------------------------
| `elements/get-categories-input-html` | `categories/input-html`
| `elements/get-modal-body`            | `element-selector-modals/body`

### Changed

The `users/save-user` action no longer includes a `unverifiedEmail` key in failure responses.

### Deprecated

| Old                        | What to do instead
| -------------------------- | ------------------
| `assets/save-asset`        | `elements/save`
| `categories/save-category` | `elements/save`
| `entries/save-entry`       | `elements/save`

## Elements

### Removed

| Old                                             | What to do instead
| ----------------------------------------------- | ---------------------------------------------------------------------
| `craft\base\Element::ATTR_STATUS_CONFLICTED`    |
| `craft\base\Element::getFieldStatus()`          | Fields’ `getStatus()` methods
| `craft\base\Element::getHasFreshContent()`      | `getIsFresh()`
| `craft\base\Element::getIsProvisionalDraft()`   | `$isProvisionalDraft`
| `craft\base\Element::getIsUnsavedDraft()`       | `getIsUnpublishedDraft()`
| `craft\base\Element::isDeletable()`             | `canDelete()`
| `craft\base\Element::isEditable()`              | `canView()` and `canSave()`
| `craft\base\ElementInterface::getEditorHtml()`  | Element edit forms are now exclusively driven by their field layout.
| `craft\base\ElementInterface::getIsDeletable()` | `canDelete()`
| `craft\base\ElementInterface::getIsEditable()`  | `canView()` and `canSave()`

## Events

### Changed

The `craft\gql\TypeManager::EVENT_DEFINE_GQL_TYPE_FIELDS` event is now triggered when actually resolving fields for a GraphQL type, rather than when the type is first created.

The following events have been renamed:

| Old                                                | Renamed to
| -------------------------------------------------- | --------------------------
| `craft\services\Assets::EVENT_GET_ASSET_THUMB_URL` | `EVENT_DEFINE_THUMB_URL`
| `craft\services\Assets::EVENT_GET_ASSET_URL`       | `EVENT_DEFINE_ASSET_URL`
| `craft\services\Assets::EVENT_GET_THUMB_PATH`      | `EVENT_DEFINE_THUMB_PATH`

### Deprecated

| Old                                                      | What to do instead
| -------------------------------------------------------- | ------------------------------------------------------
| `craft\models\FieldLayout::EVENT_DEFINE_STANDARD_FIELDS` | `EVENT_DEFINE_NATIVE_FIELDS`

### Removed

| Old                                                               | What to do instead
| ----------------------------------------------------------------- | -------------------------------------------------
| `craft\base\Element::EVENT_DEFINE_IS_DELETABLE`                   | `EVENT_AUTHORIZE_DELETE`
| `craft\base\Element::EVENT_DEFINE_IS_EDITABLE`                    | `EVENT_AUTHORIZE_VIEW` and `EVENT_AUTHORIZE_SAVE`
| `craft\controllers\EntriesController::EVENT_PREVIEW_ENTRY`        |
| `craft\services\Drafts::EVENT_AFTER_MERGE_SOURCE_CHANGES`         |
| `craft\services\Drafts::EVENT_AFTER_PUBLISH_DRAFT`                |
| `craft\services\Drafts::EVENT_BEFORE_MERGE_SOURCE_CHANGES`        |
| `craft\services\Drafts::EVENT_BEFORE_PUBLISH_DRAFT`               |
| `craft\services\Gql::EVENT_REGISTER_GQL_PERMISSIONS`              | `EVENT_REGISTER_GQL_SCHEMA_COMPONENTS`
| `craft\services\TemplateCaches::EVENT_AFTER_DELETE_CACHES`        |
| `craft\services\TemplateCaches::EVENT_AFTER_DELETE_CACHES`        |
| `craft\services\TemplateCaches::EVENT_BEFORE_DELETE_CACHES`       |
| `craft\services\Volumes::EVENT_REGISTER_VOLUME_TYPES`             |
| `craft\web\twig\variables\CraftVariable::EVENT_DEFINE_COMPONENTS` | `EVENT_INIT`

## Controller Actions

### Removed

| Old                                       | What to do instead
| ----------------------------------------- | ------------------------------------------------------
| `assets/edit-asset`                       |
| `assets/generate-thumb`                   | `assets/thumb`
| `categories/delete-category`              | `elements/delete`
| `categories/edit-category`                |
| `categories/preview-category`             |
| `categories/share-category`               |
| `categories/view-shared-category`         |
| `dashboard/get-feed-items`                |
| `edit/by-id`                              | `elements/edit`
| `edit/by-uid`                             | `elements/edit`
| `elements/get-editor-html`                |
| `elements/save-element`                   | `elements/save`
| `entries/delete-entry`                    | `elements/delete`
| `entries/delete-for-site`                 | `elements/delete-for-site`
| `entries/duplicate-entry`                 | `elements/duplicate`
| `entries/switch-entry-type`               |
| `entry-revisions/create-draft`            | `elements/create`
| `entry-revisions/delete-draft`            | `elements/delete-draft`
| `entry-revisions/publish-draft`           | `elements/apply-draft`
| `entry-revisions/revert-entry-to-version` | `elements/revert`
| `entry-revisions/save-draft`              | `elements/save-draft`
| `users/get-remaining-session-time`        | `users/session-info`

### Controller Requests & Responses

Craft 4 improves controller request and response handling to be much simpler and more uniform.

#### Action Requests

On the front end, we recommend replacing deprecated `Craft.postActionRequest()` calls with `Craft.sendActionRequest()`:

```javascript
// Craft 3
Craft.postActionRequest('my-plugin/do-something', data, function(response, textStatus) {
  if (textStatus === 'success') {
    // ...
  } else {
    // Handle errors
  }
});

// Craft 4
Craft.sendActionRequest('POST', 'my-plugin/do-something', {data})
  .then((response) => {
      // ...
  })
  .catch(({response}) => {
      // Handle non-200 responses ...
  });
```

The `sendActionRequest()` method returns a response object that includes `config`, `data`, `headers`, `request`, `status`, and `statusText` properties, and non-`200` responses can be handled with a `catch()` handler.

#### Controller Responses

Controller actions can return new [asSuccess()](craft4:craft\web\Controller::asSuccess()) / [asModelSuccess()](craft4:craft\web\Controller::asModelSuccess()) or [asFailure()](craft4:craft\web\Controller::asFailure()) / [asModelFailure()](craft4:craft\web\Controller::asModelFailure()) functions that automatically handle…

- checking whether the request accepts JSON and returning a JSON response accordingly
- returning relevant HTTP status codes
- returning an accompanying message in the JSON response or flash
- returning relevant model data in the JSON response or route params
- honoring a redirect provided via argument or request param
- including `'success' => true` and `'success' => false`

::: tip
A JSON error response will now be returned with a `400` HTTP status in Craft 4.
:::

Using these new methods, most controller action methods can be shortened:

```php
// Craft 3
public function actionSave() {
    // ...

    if (!Craft::$app->getElements()->saveElement($myElement)) {
        if ($this->request->getAcceptsJson()) {
            return $this->asJson([
                'success' => false,
                'errors' => $myElement->getErrors(),
            ]);
        }

        $this->setFailFlash(Craft::t('myPlugin', 'Couldn’t save element.'));

        Craft::$app->getUrlManager()->setRouteParams([
            'myElement' => $myElement,
        ]);

        return null;
    }

    if ($this->request->getAcceptsJson()) {
        return $this->asJson([
            'success' => true,
            'id' => $myElement->id,
            'title' => $myElement->title,
            'url' => $myElement->getUrl(),
        ]);
    }

    $this->setSuccessFlash(Craft::t('myPlugin', 'Element saved.'));
    return $this->redirectToPostedUrl($myElement);
}

// Craft 4
public function actionSave() {
    // ...

    if (!Craft::$app->getElements()->saveElement($myElement)) {
        return $this->asModelFailure(
            $myElement,
            Craft::t('myPlugin', 'Couldn’t save element.'),
            'myElement'
        );
    }

    return $this->asModelSuccess(
        $asset,
        Craft::t('app', 'Asset saved.'),
        data: [
            'id' => $myElement->id,
            'title' => $myElement->title,
            'url' => $myElement->getUrl(),
        ],
    );
}
```

[asErrorJson()](craft4:craft\web\Controller::asErrorJson()) has been deprecated in Craft 4 and will be removed in Craft 5. Use [asFailure()](craft4:craft\web\Controller::asFailure()) instead:

```php
// Craft 3
return $this->asErrorJson('Thing not found.');

// Craft 4
return $this->asFailure('Thing not found.');
```

## Filesystems

We’ve decoupled file operations from storage volumes into a new concept called “Filesystems” ([#10367](https://github.com/craftcms/cms/pull/10367)). Former volume type classes are now filesystem types, and a single [craft\models\Volume](craft4:craft\models\Volume) class represents all volumes.

## Image Transforms and Transformers

Asset Transforms are now Image Transforms, which utilize the newly-added concept of “Imager Transformers”. Existing image transform functionality and index management has been rolled into a default [ImageTransformer](craft4:craft\imagetransforms\ImageTransformer).

Third parties can introduce image transformers by registering them via the [`EVENT_REGISTER_IMAGE_TRANSFORMERS`](craft4:craft\services\ImageTransforms::EVENT_REGISTER_IMAGE_TRANSFORMERS) event.

## Unified Element Editor

Craft 4’s new unified element editing experience refines formerly-disparate UI and functionality for a number of benefits:

- Easier draft, revision, autosave, preview, and conditional field layout support for all element types.
- Drafts and autosaving in element editor slideouts for element types that support it.
- A more consistent editing experience for content authors, whether they’re editing in a full-page layout or a slideout.

One immediate benefit is to categories, which now support drafts, autosave, and editing features formerly limited to entries.

With this new unified editing experience, we’ve eliminated a variety of element-specific templates, template hooks, and controller actions that are replaced by more uniform alternatives. Similarly, new element type methods and front-end components can be utilized by all element types.

https://github.com/craftcms/cms/pull/10467

## Symfony Mailer

Craft 4 uses [Symfony Mailer](https://symfony.com/doc/current/mailer.html) to send email.

This shouldn’t require any changes to composing and sending messages, but any transport adapter’s `defineTransport()` method must now return either a transporter that implements [Symfony\Component\Mailer\Transport\TransportInterface](https://github.com/symfony/mailer/blob/6.0/Transport/TransportInterface.php), or an array that defines one.

## User Permissions

A number of user permissions have been renamed in Craft 4:

| Old                                   | New 
| ------------------------------------- | -------------------------
| `createFoldersInVolume:<uid>`         | `createFolders:<uid>`
| `deleteFilesAndFoldersInVolume:<uid>` | `deleteAssets:<uid>`
| `deletePeerFilesInVolume:<uid>`       | `deletePeerAssets:<uid>`
| `editEntries:<uid>`                   | `viewEntries:<uid>`
| `editImagesInVolume:<uid>`            | `editImages:<uid>`
| `editPeerEntries:<uid>`               | `viewPeerEntries:<uid>`
| `editPeerFilesInVolume:<uid>`         | `savePeerAssets:<uid>`
| `editPeerImagesInVolume:<uid>`        | `editPeerImages:<uid>`
| `publishEntries:<uid>`                | `saveEntries:<uid>`<br>No longer differentiates between enabled and disabled entries. Users with `viewEntries:<uid>` permissions can still create drafts.
| `publishPeerEntries:<uid>`            | `savePeerEntries:<uid>`<br>No longer differentiates between enabled and disabled entries. Users with `viewPeerEntries:<uid>` permissions can still create drafts.
| `replaceFilesInVolume:<uid>`          | `replaceFiles:<uid>`
| `replacePeerFilesInVolume:<uid>`      | `replacePeerFiles:<uid>`
| `saveAssetInVolume:<uid>`             | `saveAssets:<uid>`
| `viewPeerFilesInVolume:<uid>`         | `viewPeerAssets:<uid>`
| `viewVolume:<uid>`                    | `viewAssets:<uid>`

Some user permissions have been split into more granular alternatives:

| Old                          | Split Into
| ---------------------------- | -------------
| `editCategories:<uid>`       | `viewCategories:<uid>`<br>`saveCategories:<uid>`<br>`deleteCategories:<uid>`<br>`viewPeerCategoryDrafts:<uid>`<br>`savePeerCategoryDrafts:<uid>`<br>`deletePeerCategoryDrafts:<uid>`
| `editPeerEntryDrafts:<uid>`  | `viewPeerEntryDrafts:<uid>`<br>`savePeerEntryDrafts:<uid>`

## Writing an Upgrade Migration

- JSON column support: https://github.com/craftcms/cms/pull/9089
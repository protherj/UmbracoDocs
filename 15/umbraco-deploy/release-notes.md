---
description: >-
  Get an overview of the things changed and fixed in each version of Umbraco
  Deploy.
---

# Release notes

In this section we have summarised the changes to Umbraco Deploy and [Deploy Contrib](https://github.com/umbraco/Umbraco.Deploy.Contrib) released in each version. Each version is presented with a link to the [Deploy issue tracker](https://github.com/umbraco/Umbraco.Deploy.Issues/issues) showing a list of issues resolved in the release. We also link to the individual issues themselves from the detail.

If there are any breaking changes or other issues to be aware of when upgrading they are also noted here.

{% hint style="info" %}
If you are upgrading to a new major version you can find the details about the breaking changes in the [version specific updates](upgrades/version-specific.md) article.
{% endhint %}

## Release History

This section contains the release notes for Umbraco Deploy 15 including all changes for this version.

#### [15.0.0-rc1](https://github.com/umbraco/Umbraco.Deploy.Issues/issues?q=is%3Aissue+is%3Aclosed+label%3Arelease%2F15.0.0) (October 11th 2024)

* Compatibility with Umbraco 15
  * See full details of breaking changes under the [Version-specific Upgrade Guide](upgrades/version-specific.md)
* Removed `AcceptInvalidCertificates` setting (configure `DeployHttpClient` typed client instead)
* Removed `RemoteTreeNode` model (use `RemoteTreeEntity` instead) and `TryParseEntityIdFromNodeIdDelegate` (use `TryParseUdiRangeFromNodeIdDelegate` instead) in `DeployTransferRegisteredEntityTypeDetail`
* Added `MigrateAsync(...)` method to `IPropertyTypeMigrator` and updated `PropertyTypeMigratorBase` and implementations to use async instead

## Deploy Contrib

#### [15.0.0-rc1](https://github.com/umbraco/Umbraco.Deploy.Contrib/releases/tag/release-15.0.0-rc1) (October 14th 2024)

* Compatibility with Umbraco 15 and Deploy 15
* Removed obsolete (and unused) legacy `PrevaluePropertyValueArtifactMigratorBase`, `CheckBoxListPropertyValueArtifactMigrator` `DropDownListFlexiblePropertyValueArtifactMigrator` and `RadioButtonListPropertyValueArtifactMigrator` migrators, as they are replaced with `PrevalueArtifactMigrator` and property type migrators that support nested/recursive properties (see PR [#71](https://github.com/umbraco/Umbraco.Deploy.Contrib/pull/71))
* Removed `MediaPickerDataTypeArtifactMigrator`, as it is replaced with `ReplaceMediaPickerDataTypeArtifactMigrator` and `DefaultLegacyDataTypeConfigurationArtifactMigrator`
* Removed `MultiNodeTreePickerDataTypeArtifactMigrator`, as it is replaced with `DefaultLegacyDataTypeConfigurationArtifactMigrator`

## Legacy release notes

You can find the release notes for versions out of support in the [Legacy documentation on Github](https://github.com/umbraco/UmbracoDocs/blob/umbraco-eol-versions/11/umbraco-deploy/release-notes.md) and [Umbraco Deploy Package page](https://our.umbraco.com/packages/developer-tools/umbraco-deploy/)

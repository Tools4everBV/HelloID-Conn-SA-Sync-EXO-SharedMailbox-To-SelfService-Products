# Change Log

All notable changes to this project will be documented in this file. The format is based on [Keep a Changelog](https://keepachangelog.com), and this project adheres to [Semantic Versioning](https://semver.org).

## [3.0.2] - 2026-08-11

### Fixed

- **Action Preservation on Property Updates**: Fixed critical issue where product actions were lost when updating only product properties (name, description, etc.) without action update settings enabled. The script now correctly preserves all existing actions during property-only updates.

## [3.0.1] - 2026-07-29

### Fixed
- **CRITICAL**: Corrected `OwnershipMaxDuration` description from "days" to "seconds" with correct value (31536000 seconds = 365 days). Previous incorrect value of 365 would have resulted in products expiring after only 6 minutes instead of 1 year.
- Update Threshold comment now correctly mentions that it applies when actions or access groups updates are enabled, not only when `$overwriteExistingProduct = $true`

### Improved
- Access Groups documentation expanded with clearer explanation that source can be any configured source in HelloID (e.g., "AzureAD", "ActiveDirectory", etc.), not just "local" or "AzureAD"
- Update Behavior section enhanced with IMPORTANT notice about performance impact and best practices
- Resource Owner Group deletion comment clarified to mention all synced groups (from e.g. AD or Entra ID), not only AzureAD groups
- Update Resource Owner Group on name change comment simplified for better clarity

## [3.0.0] - 04-06-2026

### Added
- **Test Run Mode**: Added test run settings to limit operations per type (creates, updates, deletes) for safer testing
- **Safety Thresholds**: Added configurable thresholds for create, update, and remove operations to prevent accidental mass changes
- **Configurable Remove Behavior**: Added three options for handling products no longer in source system:
  - `None`: Keep products (requires manual cleanup)
  - `Disable`: Disable products (reversible, recommended)
  - `Remove`: Permanently delete products (irreversible)
- **Resource Owner Group Cleanup**: Option to automatically remove resource owner groups when products are removed
- **Product Configuration Function**: New `New-HelloIDProductConfiguration` function with comprehensive inline documentation for all product properties
- **Granular Property Updates**: Added ability to specify exactly which product properties to update on existing products
- **Access Group Update Behavior**: Three modes for updating access groups (None, Add, Replace)
- **Mailbox Property Selection**: Configurable list of mailbox properties to retrieve from Exchange Online
- **Category Auto-Creation**: Option to automatically create product categories if they don't exist
- **Product Lifecycle Actions**: Expanded support for all lifecycle actions (onRequest, onApprove, onDeny, onReturn, onWithdrawn)
- **Action Management**: Granular control over updating, adding, and removing product actions
- **Update Resource Owner on Name Change**: Option to rename resource owner groups when source object names change

### Changed
- **BREAKING**: Complete restructuring of configuration with organized sections:
  - Connection Configuration
  - Script Behavior
  - Product Lifecycle
  - Source Data Selection
  - Product Identification
  - Product Configuration Function
  - Resource Owner Configuration
  - Update Behavior
- **BREAKING**: Renamed configuration variables for clarity and consistency:
  - `$ProductSkuPrefix` → `$productIdentifierPrefix`
  - `$exchangeMailboxUniqueProperty` → `$sourceObjectUniqueProperty`
  - `$productResourseOwner` → `$productResourceOwner`
  - `$calculateProductResourceOwnerPrefixSuffix` → `$resourceOwnerMode` (now "Fixed" or "Calculated")
  - `$overwriteAccessGroup` → `$accessGroupUpdateBehavior`
- **BREAKING**: Product configuration now uses a function-based approach instead of inline variables
- **BREAKING**: Action scripts now referenced by variable name to keep configuration clean
- Enhanced inline documentation with detailed explanations for every configuration option
- Improved resource owner mode configuration with clearer "Fixed" vs "Calculated" terminology
- Better organization of update behavior settings with clear warnings
- Standardized variable naming conventions throughout the script

### Improved
- Comprehensive inline documentation for all configuration sections
- Clear warnings and recommendations for potentially dangerous operations
- Better structured sections with clear separation of concerns
- More intuitive configuration with examples and best practices
- Enhanced safety with multiple threshold options and test run mode

### Fixed
- Configuration consistency issues with resource owner group management
- Unclear update behavior options now clearly documented

## [2.2.1] - 15-05-2026

### Fixed
- Use GUID property instead of object references for mailbox identification to improve stability and consistency.

## [2.2.0] - 04-05-2026

### Changed
- Authentication to certificate based.

## [2.1.2] - 08-01-2026

### Changed
- fix: `managerCanOverrideDuration` default to `false`.

## [1.0.0] - 08-11-2023
This is the first official release of _HelloID-Conn-SA-Sync-EXO-SharedMailbox-To-SelfService-Products_.

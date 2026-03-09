---
title: "Organization profile manifest reference"
url: "https://docs.snowflake.com/en/user-guide/collaboration/organization-profiles/org-profile-manifest-reference"
---

# Organization profile manifest reference

Creating organization profiles programmatically requires a manifest, written in YAML (<https://yaml.org/spec/>). Use the information provided here to learn about the parameters available in an organization profile manifest.

## Organization profile manifest

CopyExpand

```
#
# Organization profile manifest
#
title: <organization_profile_title>
description: <organization_profile_description>
contact: <organization_profile_contact>
approver_contact: <organization_profile_approver_contacts>
allowed_publishers:
  access:
      - account: account_name1
        roles: [<roles_list>]
      - account: account_name2
logo: <organization_profile_logo_urn>
```

Show lessSee more

Scroll to top

## Organization profile fields

The parameters within the organization manifest allow you to create organization profiles for specific organizational listings. Required and optional fields are identified.

`title` (Required)
:   String. The organization profile title. This field represents the Provider domain. It’s shown under the Organization Listing and as a filter option under Providers in an Internal Marketplace.

    > CopyExpand
    >
    > ```
    > . . .
    > title: "Title"
    > . . .
    > ```
    >
    > Show lessSee more
    >
    > Scroll to top

`description` (Required)
:   String. A description for the organization profile.

    > CopyExpand
    >
    > ```
    > . . .
    > description: "Description"
    > . . .
    > ```
    >
    > Show lessSee more
    >
    > Scroll to top

`contact` (Required)
:   String. The email address of the organization profile owner.

    CopyExpand

    ```
    . . .
    contact: "contact@snowflake.com"
    . . .
    ```

    Show lessSee more

    Scroll to top

`approver_contact` (Required)
:   String. The email address of the organization profile approver.

    > The following is an example of the format:
    >
    > CopyExpand
    >
    > ```
    > . . .
    > approver_contact: "approver_contact@snowflake.com"
    > . . .
    > ```
    >
    > Show lessSee more
    >
    > Scroll to top

`allowed_publishers` (Optional)
:   The accounts that are allowed to publish the listing associated with the organization profile. You must specify the following with `allowed_publishers`:

    * `access`: A list of accounts allowed to publish the listing associated with the organization profile. To allow all accounts to publish the listing associated with the organization profile, use `all_internal_accounts: "true"`. To specify a list of roles within the current account that can access a profile, use `roles`.

      Note

      You can only assign specific roles in the current account.

    The following is an example of the format:

    CopyExpand

    ```
    . . .
    allowed_publishers:
      access:
        - account: "account_name1"
          roles: ['PUBLIC']
        - account: "account_name2"
    . . .
    ```

    Show lessSee more

    Scroll to top

`logo` (Optional)
:   String. The URN for the organization profile icon or emoji. Use the following format to specify a logo: `logo: "urn:icon:<name>:<color>"`

    The following table lists the available icons:

    | Icon | Name | Icon | Name |
    | --- | --- | --- | --- |
    | ../../../_images/icon_ai.png | ai | ../../../_images/icon_blocks.png | blocks |
    | ../../../_images/icon_book.png | book | ../../../_images/icon_calendar.png | calendar |
    | ../../../_images/icon_classification.png | classification | ../../../_images/icon_code.png | code |
    | ../../../_images/icon_compute.png | compute | ../../../_images/icon_dataengineering.png | dataengineering |
    | ../../../_images/icon_diamond.png | diamond | ../../../_images/icon_energy.png | energy |
    | ../../../_images/icon_environment.png | environment | ../../../_images/icon_forecasting.png | icon\_forecasting |
    | ../../../_images/icon_gear.png | gear | ../../../_images/icon_government.png | government |
    | ../../../_images/icon_healthmedicine.png | healthmedicine | ../../../_images/icon_healthscience.png | healthscience |
    | ../../../_images/icon_language.png | language | ../../../_images/icon_legal.png | legal |
    | ../../../_images/icon_loudspeaker.png | loudspeaker | ../../../_images/icon_machinelearning.png | machinelearning |
    | ../../../_images/icon_marketplaceinternal.png | marketplaceinternal | ../../../_images/icon_package.png | package |
    | ../../../_images/icon_personalinfo.png | personalinfo | ../../../_images/icon_pin.png | pin |
    | ../../../_images/icon_pinbuilding.png | pinbuilding | ../../../_images/icon_pindata.png | pindata |
    | ../../../_images/icon_pinglobe.png | pinglobe | ../../../_images/icon_pinmap.png | pinmap |
    | ../../../_images/icon_public.png | public | ../../../_images/icon_scale.png | scale |
    | ../../../_images/icon_shieldlock.png | shieldlock | ../../../_images/icon_sport.png | sport |
    | ../../../_images/icon_team.png | team | ../../../_images/icon_transportation.png | transportation |
    | ../../../_images/icon_travel.png | travel | ../../../_images/icon_weather.png | weather |
    | ../../../_images/icon_writinghand.png | writinghand |  |  |

    See moreShow less

    Expand

    Available logo colors include:

    * Default (Grey)
    * Blue
    * Violet
    * Pink
    * Orange
    * Aqua

    The following is an example of the format:

    CopyExpand

    ```
    . . .
    logo: "urn:icon:shieldlock:blue"
    . . .
    ```

    Show lessSee more

    Scroll to top

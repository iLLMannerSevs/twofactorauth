# Contributing to 2fa.directory

All the data is managed through a series of [JSON][json] files so it may be
useful to read up on the JSON syntax.

To add a new site, go to the [data files][entries] and get familiar with how it
is set up. There is one file per entry named after the domain, located in the
subdirectory starting with the first letter of the domain. Site icons
are stored in folders corresponding to each of those entries in their own
[folder][img]. Please ensure any JSON files that are edited conform to our [`.editorconfig`][editorconfig].

## Guidelines

1. **Don't break the build**: We have a simple continuous integration system
   setup with GitHub Actions. If your pull request doesn't pass, it won't be
   merged. GH Actions will only check your changes after you submit a pull request.
2. **Use a Nice Icon**: SVG is the preferred format. If possible, please also run the image
   through an optimizing utility such as [svgo][svgo] (command line) or [SVGOMG][SVGOMG] (web) to reduce the file size.
   If an SVG icon is not available, the icon should be a PNG with a resolution of 32x32, 64x64, or 128x128.
   If possible, run the PNG image through an optimizing utility such as [TinyPNG][tinypng] so that it ideally is below 3 kB in size.
3. **HTTPS links**: All sites that support HTTPS should also be linked with an HTTPS address.
4. **Similarweb top 200K**: A new site that is not already listed has to be within the
   Similarweb top 200,000 global rankings. You can check the ranking of a site [here][similarweb].
5. **No 2FA providers**: We do not list 2FA providers, such as [Authy][authy], [Duo][duo] or
   [Google Authenticator][googleauthenticator].

## Site Criteria

The following section contains rough criteria and explanations regarding
what websites should be listed in 2fa.directory. If one of the following
criteria is met, it belongs in 2fa.directory:

1. **Personal Info**: Any site that deals with personally identifiable information (PII) such as name, photo, age, address, etc.
2. **Data**: This criterion relates to data that is either important or sensitive.
   Websites detailed in Criteria 1 also fit this criteria.
3. **Money**: Any site that deals with money.
4. **Control**: This criteria is more general, in that it includes sites that
   give access to things that may infringe upon criteria 1, 2, or 3. An example
   of this is a website that allows remote access to a device.

If you have any questions regarding whether or not a site matches one of the
criteria, simply open an issue and we'll take a look.

### Excluded Sites

A list of excluded sites and categories has also been created with various categories and sites that we have opted not
to list on 2fa.directory.
You should check the list in the [EXCLUSION.md file][exclude] to make sure that your site is eligible before submitting
a pull request.

## New Sites

First and foremost, make sure the new site meets our [definition
requirements][definitions] of two-factor authentication.

If you are adding multiple sites to the 2FA Directory, please create a new
git branch for each website, and submit a separate pull request for each branch.
More information regarding how to create new git branches can be found on
[GitHub's Help Page][github-tutorial].

Adding a new website should be pretty straightforward. Create a JSON file in
the corresponding [subdirectory][entries] as shown in the following example:

```JSON
{
  "Site Name": {
    "domain": "site.com",
    "img": "site.com.png",
    "tfa": [
      "sms",
      "call",
      "email",
      "totp",
      "u2f",
      "custom-software",
      "custom-hardware"
    ],
    "documentation": "<link to site TFA documentation>",
    "categories": [
      "category1",
      "category2"
    ]
  }
}
```

- The domain should point to the main page of the service, not the login page (usually the root domain, not a
  subdomain).
- Categories must be selected from the values listed in [`categories.json`][categories].
- The default value for the icon is `<domain>.svg`, but can be overridden by an `img`
  field.
- If you would like the site's link on 2fa.directory to be different from `https://<domain>`, you can use a `url` field to
  specify this.

#### Adding a site that _supports_ TFA

Sites that provide TFA can be noted with the `tfa` field and should contain the TFA methods supported.
If a site does provide TFA, it is strongly recommended that you add the `documentation`
field where public documentation is available.
Sites supporting TFA must not have a `contact` property.

The following is an example of a website that _supports_ TFA:

```JSON
{
  "YouTube": {
    "domain": "youtube.com",
    "tfa": [
      "sms",
      "call",
      "totp",
      "custom-software",
      "u2f"
    ],
    "documentation": "https://www.google.com/intl/en-US/landing/2step/features.html",
    "categories": [
      "entertainment"
    ]
  }
}
```

#### Adding a site that _does not_ support TFA

If a site does not provide TFA, the `contact` field should be included.
Inside this object,

- The `twitter` field should be included if the site uses Twitter.
- Facebook can also be included using the `facebook` field.
- Email can be included using the `email` field.
- Contact forms can be included using the `form` field.
- Only email or a contact form can be added, not both. Emails are preferred over
  contact forms.
- The `language` field inside `contact` can be included for websites whose social
  media pages/communication channels do not use English. The language codes should
  be lowercase [ISO 639-1][iso-lang-wikipedia] codes.

Do not add the `tfa`, `notes`, `documentation`, or `recovery` fields.

The following is an example of a website that _does not_ support TFA:

```JSON
{
  "Netflix": {
    "domain": "netflix.com",
    "contact": {
      "facebook": "netflix",
      "twitter": "Netflixhelps"
    },
    "categories": [
      "entertainment"
    ]
  }
}
```

### Exceptions & Restrictions

If a site requires the user to do something out of the ordinary to set up 2FA or if 2FA is
only available to specific account types, you can document this using the `notes` field.

```JSON
{
  "Site Name": {
    "domain": "site.com",
    "tfa": [
      "totp"
    ],
    "documentation": "<link to site TFA documentation>",
    "notes": "Specific text goes here.",
    "categories": [
      "category"
    ]
  }
}
```

### Adding a site that is only available or is prevalent in specific regions

If a site (with or without 2FA) is only available in certain countries or most users are located in certain countries -
for example a government site or a local retailer - you can note this with the `regions` field.

```JSON
{
  "Site Name": {
    "domain": "site.com",
    "tfa": [
      "totp"
    ],
    "documentation": "<link to site TFA documentation>",
    "categories": [
      "category"
    ],
    "regions": [
      "us",
      "ca"
    ]
  }
}
```

The country codes should be lowercase [ISO 3166-1][iso-country-wikipedia] codes.

If you want to distinguish the entry from others of the same company or service in order to provide a unique name,
use `"Site Name [CC]"` where `CC` is the country code.

#### Excluded Regions

If a site is available globally apart from a specific region, this can be noted using the `regions` array. Excluded
regions should be prefixed with a `-` symbol to exclude the site from that region. Region codes and excluded region
codes should **not** be used together, as adding a region code automatically excludes the site from other regions. The
example below shows a site that is available in all regions apart from `us`.

```JSON
{
  "Site Name": {
    "domain": "site.com",
    "tfa": [
      "totp"
    ],
    "documentation": "<link to site TFA documentation>",
    "categories": [
      "category"
    ],
    "regions": [
      "-us"
    ]
  }
}
```

### Other Properties

- `additional-domains`
  If a site exists at another domain in addition to the main domain that is listed in the
  `domain` field, you can mark this with the `additional-domains` property.

```JSON
{
  "Site Name": {
    "domain": "site.com",
    "additional-domains": [
      "site.net",
      "site.io"
    ],
    "tfa": [
      "totp"
    ],
    "documentation": "<link to site TFA documentation>",
    "categories": [
      "category"
    ]
  }
}
```

- `recovery`
  The recovery field can be used to link to account recovery documentation about what to do
  if you lose access to your 2FA method.

```JSON
{
  "Site Name": {
    "domain": "site.com",
    "tfa": [
      "totp"
    ],
    "documentation": "<link to site TFA documentation>",
    "recovery": "<link to site TFA recovery documentation>",
    "categories": [
      "category"
    ]
  }
}
```

- `custom-software`/`custom-hardware`
  If a site uses a proprietary software or hardware method, you can add specific details of what
  is being used. Examples would be Authy or non-U2F security keys.

```JSON
{
  "Site Name": {
    "domain": "site.com",
    "tfa": [
      "custom-software",
      "custom-hardware"
    ],
    "custom-software": [
      "Authy"
    ],
    "custom-hardware": [
      "Yubico OTP"
    ],
    "documentation": "<link to site TFA documentation>",
    "categories": [
      "category"
    ]
  }
}
```

### Pro Tips

- See Guideline #2 about icons. The SVG file should go in the corresponding
  `img/` folder.

- For the sake of organization and readability, it is appreciated if your site chunk
  follows the same order as the example earlier in the document.

- If a site supports TFA, their contact information is not needed and must be left out.

## Categories

Entries on 2fa.directory have to be put into categories. An entry can have one or many categories.
Below is a table of each available category, the name of the category, which title it has on 2fa.directory with a
description.

| Name             |           Title           | Description                                                                         |
|------------------|:-------------------------:|-------------------------------------------------------------------------------------|
| backup           |      Backup and Sync      | Online backup and cross-device file synchronization                                 |
| banking          |          Banking          | Online banking platforms                                                            |
| betting          |          Betting          | Betting and gambling                                                                |
| cloud            |      Cloud Computing      | "Serverless" cloud computing                                                        |
| communication    |       Communication       | Online communication platforms excluding email and social media                     |
| creativity       |        Creativity         | Art and design software                                                             |
| crowdfunding     |       Crowdfunding        |                                                                                     |
| cryptocurrencies |     Cryptocurrencies      | Any site whose main purpose is handling cryptocurrencies                            |
| developer        |         Developer         | Development software                                                                |
| domains          |          Domains          | DNS registrars                                                                      |
| education        |         Education         | Non-university education platforms                                                  |
| email            |           Email           | Email providers                                                                     |
| entertainment    |       Entertainment       | Audio/Video entertainment excluding games                                           |
| finance          |          Finance          | Financial and pension services                                                      |
| food             |           Food            | Food and beverage services                                                          |
| gaming           |          Gaming           | Games and game platforms. Sites for buying games should be listed in Retail         |
| government       |        Government         | Government portals. Excluding education                                             |
| health           |          Health           | Health and fitness platforms                                                        |
| hosting          |        Hosting/VPS        | Online website hosting, VPS, and dedicated server rentals                           |
| identity         |    Identity Management    | Authentication providers, Single Sign On platforms                                  |
| insurance        |         Insurance         | Insurance services                                                                  |
| investing        |         Investing         | Investment platforms                                                                |
| iot              |            IoT            | Internet of Things and device management platforms                                  |
| legal            |           Legal           | Legal aid services                                                                  |
| marketing        |   Marketing & Analytics   | Marketing campaign providers and analytics services                                 |
| payments         |         Payments          | Payment providers                                                                   |
| postal           |     Post and Shipping     | Postal and logistics providers                                                      |
| remote           |       Remote Access       | Remote device access platforms                                                      |
| retail           |          Retail           | Online retail platforms                                                             |
| security         |         Security          | Online security and anti-malware services. Excluding VPN and identity management    |
| social           |          Social           | Social networks                                                                     |
| task             |      Task Management      | Task management and to-do services                                                  |
| tickets          |    Tickets and Events     | Ticketing and event platforms                                                       |
| transport        |         Transport         | Public transport and taxis.                                                         |
| travel           | Travel and Accommodations | Services related to flights, hotels and car rentals                                 |
| universities     |       Universities        | University online platforms                                                         |
| utilities        |         Utilities         | Household utilities including electricity, gas, water, phone and internet providers |
| vpn              |       VPN Providers       |                                                                                     |
| other            |           Other           | Sites that don't fit in any other category                                          |

When adding a category to an entry remember to use the category name and not the title.
If you're unsure which category is correct for an entry, ask for assistance by creating a [new issue][issue].

## A Note on Definitions

### Authorization

There are lots of different ideas of what constitutes two-factor authentication and
what doesn't, so it stands to reason that we should clarify a bit. For the
purposes of this site, two-factor authentication is defined as any service provided as a
redundant layer for account _authentication_. Services that provide
_authorization_ redundancy are certainly appreciated, but should not be
considered two-factor authentication.

As an example, a site that prompts you for an authentication token following
user login would be considered two-factor authentication. A site that does not prompt you
for a token upon login, but prompts you for a token when you try to perform a
sensitive action would not be considered two-factor authentication.

For context, check out the discussion in issue [#242][242].

### Passwordless Authentication

Many sites now offer passwordless authentication, replacing the traditional password (something you know) with a different factor, such as something you have or are. Examples include sites allowing users to log in using a U2F key or a magic link without requiring a second factor. While these methods enhance security, it's important to note that our 2FA Directory focuses on platforms providing two distinct factors for authentication. For passkey-specific options, we invite you to explore the [Passkeys Directory][passkeys-directory]. The repository is [2factorauth/passkeys][passkeys-repository].

[json]: https://www.json.org/
[entries]: entries/
[img]: img/
[editorconfig]: .editorconfig
[svgo]: https://github.com/svg/svgo
[SVGOMG]: https://jakearchibald.github.io/svgomg/
[tinypng]: https://tinypng.com/
[similarweb]: https://www.similarweb.com/
[authy]: https://authy.com/
[duo]: https://duo.com/
[googleauthenticator]: https://github.com/google/google-authenticator
[exclude]: /EXCLUSION.md
[categories]: #categories
[issue]: https://github.com/2factorauth/twofactorauth/issues/new/choose
[definitions]: #a-note-on-definitions
[github-tutorial]: https://help.github.com/articles/creating-and-deleting-branches-within-your-repository/
[iso-lang-wikipedia]: https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes
[iso-country-wikipedia]: https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes
[242]: https://github.com/2factorauth/twofactorauth/issues/242
[passkeys-directory]: https://passkeys.2fa.directory
[passkeys-repository]: https://github.com/2factorauth/passkeys

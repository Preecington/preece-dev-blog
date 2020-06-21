---
title: "iOS TestFlight Deploy With GitHub Actions"
date: 2020-06-20T16:54:19+10:00
tags:
- GitHub Actions
- YAML
- iOS
- Xamarin
- .NET
summary: "A (not so quick) dive on getting an iOS app deployed to TestFlight using GitHub Actions"
thumbnailImage: /img/testflight_actions.png
comments: true
---
![TestFlight + Actions Header Image](/img/testflight_actions.png)

{{< alert success no-icon >}}
**TL;DR**: The `YAML` [is here]({{< ref "#show-me-the-yaml" >}}) if you want to skip to the good part.
{{< /alert >}}

I've just started a new iPhone project in my personal time, and I thought it'd be nice to get a CD pipeline setup. The idea is to get each PR merge out to my ~~victims~~ friends who are kindly testing my buggy code via TestFlight. I thought this would be pretty straightforward (it has been with Azure Pipelines in the past!), but for added fun I thought I'd do it with [GitHub Actions](https://github.com/features/actions). Easy, right?

Turns out... not so much! 😅

After a week and many hours cursing, I created a workflow that nailed it. 🎉 In the interest of giving back to the community, I've documented the steps (and the `YAML` workflow I came up with) so you too can follow along and join in on the sweet DevOps goodness. The workflow makes a new build ready for release, increments the version number, uses the last PR merge commit message as the test release notes, and sends it off for distribution to a group we nominate.

{{< alert info >}}
Whilst my solution is for Xamarin.iOS, this should cover 95% of what you need to for any release procedure that needs to build an `.ipa` package and release it via TestFlight.
{{< /alert >}}

## How'd I do this

You're going to need a few things:

1. An Apple Developer Account with a paid Developer Program subscription
2. An app registration setup in [App Store Connect](https://appstoreconnect.apple.com/) with at least **one** build that has been released externally already (that is, to an external testing group that you have defined)
3. Access to a macOS device to generate distribution certificates with private keys, if you don't have one already
4. A GitHub repo - public or private, it'll still work!

What you _won't_ need is a non-2FA Apple ID like most iOS CI approaches require, so that's a nice bonus!

### Prerequisite #1 - Distribution Certificate & Provisioning Profile

You will need to generate a `.p12` Distribution Certificate and a App Store Distribution Provisioning Profile using the Developer Portal. There are plenty of guides on how to get a `p12` export going - I used [this guide](https://support.magplus.com/hc/en-us/articles/203808748-iOS-Creating-a-Distribution-Certificate-and-p12-File) to do that. Note down the passphrase you use to export the certificate - you'll need it later.

Once you have the certificate export, you'll need to export it as `base64` for later.

```bash
openssl base64 -in Certificates.p12 -out Certificates.txt
```

If you haven't got an App Store Distribution Profile, make one now, and ensure your distribution certificate is included. You won't need to download it though.

### Prerequisite #2 - App Store Connect API Key

Next you will need to generate an App Store Connect API key with the App Manager access role, if you don't have one already. You can do that from the [Users and Access - Keys](https://appstoreconnect.apple.com/access/api) section of App Store Connect.

Once made, you should make a note of the Issuer ID, Key ID and download the private key using the _Download API Key_ link. You'll need all three of these things later. If you run into trouble, [the Apple docs on generating API keys](https://developer.apple.com/documentation/appstoreconnectapi/creating_api_keys_for_app_store_connect_api) are a good place to start.

### Prerequisite #3 - App Specific Password

In order to not get 2FA prompts when our release is happening, we'll need an app-specific password to upload builds to App Store Connect. You can set one up via [Apple Account Management](https://appleid.apple.com/account/manage) - and like the others, note it down for later.

### Prerequisite #4 - App 'Apple ID'

Last but not least, you'll need your app's Apple ID. No, _not_ your own Apple ID in email form - a numerical number that identifies your application inside App Store Connect. Navigate to the [Apps section](https://appstoreconnect.apple.com/apps/) in App Store Connect and select App Information on the left - you'll find the Apple Id under the General Information pane. That's the last thing you need!

## Building the GitHub Action Workflow

{{< alert warning >}}
As of time of writing, the App Connect API is a bit _flaky._ It has this habit of returning `403` 2-7 times in a row before actually letting you in, even though your credentials are fine. I had to hack around this - forgive the clunkiness!
{{< /alert >}}

Phew. Now that we've collected all the required parts, you can start on the action itself. Firstly head to your repo's _Secrets_ section in GitHub, and add the following secrets in:

**Name**|**Value**
-----|-----
APPCONNECT\_API\_ISSUER|Issuer ID from #2
APPCONNECT\_API\_KEY\_ID|Key ID from #2
APPCONNECT\_API\_KEY\_PRIVATE|Contents of the `.p8` file downloaded in #2
APPLE\_ID|ID from #4
DIST\_CERT\_BASE64|Contents of the `.txt` file generated in #1
DIST\_CERT\_P12\_PASSWORD|Passphrase from #1
FASTLANE\_APP\_PASSWORD|The app-specific password in #3
FASTLANE\_USERNAME|The email associated with the Apple Account you've been using

To top it all off, create a workflow file in your repo that lives in `.github/workflows/testflight.yaml`. I called it `testflight` - you can call it whatever you like.

The workflow in words is: _On **push** to `master`, perform the following on the latest macOS agent:_

1. Checkout code with full history
2. Make a temporary keychain to hold the distribution certificate
3. Build a bearer token for the Connect API
4. 'Prime' the API [(see note above)]({{< ref "#building-the-github-action-workflow" >}})
5. Import the right provisioning profile
6. Bump our build versions to use the build number
7. Do the actual build. **NB: This is Xamarin.iOS for me - replace with your build tool of choice**
8. Record the built `.ipa` as a build artefact in case we need it in the future
9. Generate release notes from our PR merge history
10. Push the build to TestFlight
11. Instruct TestFlight to release the build to our chosen external group

{{< alert info >}}
Step 11 assumes you have not set export compliance in your PList and you intend to nominate that you use non-exempt encryption. Read more [here](https://developer.apple.com/documentation/security/complying_with_encryption_export_regulations) if you're unsure.
{{< /alert >}}

## Show me the YAML

Here it is, in all it's glory. You should change the following to your own values:

* `bundle-id`
* `GROUP_NAME`

{{< alert warning >}}
Here be dragons! 🐉🐉🐉 Be warned there's a wide array of language use going on.
{{< /alert >}}

{{< gist Preecington c1c2b30582a0ecf858da0a100ba621b4 >}}

And that's it! 🎉 Some of the above is pretty darn clunky, particularly the sprinkling of Python and Bash, but it works quite well. I'd like GitHub Actions to support more tasks out of the box like Azure Pipelines does, and overall the experience of the Actions Marketplace left a bit to be desired - but that's for another post!

If you'd like more understanding of how the App Store Connect API works, I'd recommend checking out the [API docs](https://developer.apple.com/documentation/appstoreconnectapi) - it does more than just poking TestFlight! And lastly, any suggestions on how to improve the workflow would be more than welcome. ❤

Happy automating! 🚀

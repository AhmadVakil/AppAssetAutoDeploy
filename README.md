# Automate Android mobile Application Deployment Process

Automating tedious deployment of Android mobile applications to Google Play Store using Fastlane and Github Actions.

![fatlane-tools](https://github.com/AhmadVakil/AutoDeployAndroidApp/fastlane-tools.png)

## Installation

Make sure you have the latest version of the Xcode command line tools installed:

```
xcode-select --install
```

Install _fastlane_ using
```
[sudo] gem install fastlane -NV
```
or alternatively using `brew install fastlane`

# Available Actions
### test
```
fastlane test
```

### post_slack_deploy_message
```
fastlane post_slack_deploy_message
```

### update_available_plugins
```
fastlane update_available_plugins
```
----

## Workflows

Here are three different .yml file which you can use as your workflow.

- [`distribute.yml`](.github/workflows/distribute.yml) - For Firebase Distribution.
- [`releaseBeta.yml`](.github/workflows/releaseBeta.yml) - Deploying to beta track to the Google Play Store.
- [`releaseProd.yml`](.github/workflows/releaseProd.yml) - Deploying to production track to the Google Play Store.

## Fastlane

#### - Fastfile

```ruby
   default_platform(:android)
   
   platform :android do
   
       desc "Firebase App Distributions"
       lane :distribute do
           gradle(task: "clean assembleRelease")
           firebase_app_distribution(
               service_credentials_file: "firebase_credentials.json",
               app: ENV['FIREBASE_APP_ID'],
               release_notes_file: "FirebaseAppDistributionConfig/release_notes.txt",
               groups_file: "FirebaseAppDistributionConfig/groups.txt"
           )
           slack(message: "Firebase App Distributed!")
       end
   
       desc "Deploy to the Google Play"
       lane :beta do
           gradle(task: "clean bundleRelease")
           upload_to_play_store(track: 'beta', release_status: 'draft')
           slack(message: "BETA successfully deployed!")
       end
   
       desc "Deploy a new version to the Google Play"
       lane :production do
           gradle(task: "clean bundleRelease")
           upload_to_play_store(release_status: 'draft')
           slack(
             message: "App successfully released!",
             channel: "#channel",  # Optional, by default will post to the default channel configured for the POST URL.
             success: true,        # Optional, defaults to true.
             payload: {  # Optional, lets you specify any number of your own Slack attachments.
               "Build Date" => Time.new.to_s,
               "Built by" => "Jenkins",
             },
             default_payloads: [:git_branch, :git_author], # Optional, lets you specify default payloads to include. Pass an empty array to suppress all the default payloads.
             attachment_properties: { # Optional, lets you specify any other properties available for attachments in the slack API (see https://api.slack.com/docs/attachments).
                  # This hash is deep merged with the existing properties set using the other properties above. This allows your own fields properties to be appended to the existing fields that were created using the `payload` property for instance.
               thumb_url: "http://example.com/path/to/thumb.png",
               fields: [{
                 title: "My Field",
                 value: "My Value",
                 short: true
               }]
             }
           )
       end
   end

```
#### - Slack



Send a success/error message to your [Slack](https://slack.com) group




> Create an Incoming WebHook and export this as `SLACK_URL`. Can send a message to **#channel** (by default), a direct message to **@username** or a message to a private group **group** with success (green) or failure (red) status.


slack ||
---|---
Supported platforms | ios, android, mac
Author | @KrauseFx



## 2 Examples

```ruby
slack(message: "App successfully released!")
```

```ruby
slack(
  message: "App successfully released!",
  channel: "#channel",  # Optional, by default will post to the default channel configured for the POST URL.
  success: true,        # Optional, defaults to true.
  payload: {  # Optional, lets you specify any number of your own Slack attachments.
    "Build Date" => Time.new.to_s,
    "Built by" => "Jenkins",
  },
  default_payloads: [:git_branch, :git_author], # Optional, lets you specify default payloads to include. Pass an empty array to suppress all the default payloads.
  attachment_properties: { # Optional, lets you specify any other properties available for attachments in the slack API (see https://api.slack.com/docs/attachments).
       # This hash is deep merged with the existing properties set using the other properties above. This allows your own fields properties to be appended to the existing fields that were created using the `payload` property for instance.
    thumb_url: "http://example.com/path/to/thumb.png",
    fields: [{
      title: "My Field",
      value: "My Value",
      short: true
    }]
  }
)
```


## Status

Distribution

![Distribute](https://github.com/AhmadVakil/AutoDeployAndroidApp/workflows/Distribute/badge.svg)

Beta Deployment
                                                                          
![Release (Beta)](https://github.com/AhmadVakil/AutoDeployAndroidApp/workflows/Release%20(Beta)/badge.svg) 

Production Deployment
                                                                        
![Release (Production)](https://github.com/AhmadVakil/AutoDeployAndroidApp/workflows/Release%20(Production)/badge.svg)


## Tools used

- [GitHub Actions CI/CD](https://github.com/features/actions)
- [Firebase App Distribution](https://firebase.google.com/docs/app-distribution)
- [Fastlane](https://fastlane.tools/)


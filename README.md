# ScaLabs GitHub Actions

GitHub Action Snippets & Templates, used in our Projects at ScaLabs

## Usage

The GitHub Action `.yaml` files must be copied to `.github/workflows/<FILENAME>.yaml`

## Fastlane
- if needed: export GEM_HOME="$HOME/.gem"
- gem install bundler
- run: 'bundle update' in the root folder
- git add Gemfile and 'Gemfile.lock' 
- run 'bundle exec fastlane init' in '/android'
- add firebase_app_distribturion plugin

Setup Firebase App Distribution

Open firebase console https://console.firebase.google.com/
- Select Add project and enter your project name
- Enable Google Analytics if required
- Click on Android icon to add project
- Enter package name (application id) and register app
- Download config file and add it to your Android project (inside android/app folder)
- Select next and continue to console.
- Now if you select your new Android app from the top, and select the settings gear icon, you can scroll down to find your Android App ID which we’ll need later

Setting up Firebase Service Credentials

- Firebase service credentials is required for authentication.
- To download credentials go to Google Cloud Platform console -> select your project -> create a new service account.
- Add the ‘Firebase App Distribution Admin’ role
- Create a private JSON key and download it and keep it safe as it is required in next step for authentication
  
Encoding the firebase credentials json
- base64 -i firebase_credentials.json > firebase_credentials.json.b64

Setup Fastfiles
- Open the ./android/fastlane/Fastfile and enter the following under the auto-generated comments:

default_platform(:android)

platform :android do
  desc "Upload Android App to Firebase"
  lane :distribute_android_app do
      firebase_app_distribution(
          app: ENV["FIREBASE_APP_ID_ANDROID"],
          testers: "<<YOUR_MAIL_ID>>",
          release_notes_file: "<<PATH_TO_RELEASE_NOTES_FLE>>",
          android_artifact_type: "APK",
          android_artifact_path: "<<PATH_TO_APK>>",
          service_credentials_file: "firebase_credentials.json"
      )
  end
end

- app: firebase app id got from previous step
- testers: list of mailids
- release_notes_file: path to the release notes text file
- android artifact type: APK or AAB
- service_credentials_file: firebase credentials json file path

Storing keystore signing keys

def keystoreProperties = new Properties()
def keystorePropertiesFile = rootProject.file('key.properties')
if (keystorePropertiesFile.exists()) {
    keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
}
android {
signingConfigs {
    release {
        keyAlias keystoreProperties['keyAlias']
        keyPassword keystoreProperties['keyPassword']
        storeFile rootProject.file(<<JKS_FILENAME>>)
        storePassword keystoreProperties['storePassword']
    }
}
}

GitHub Action secrets: 

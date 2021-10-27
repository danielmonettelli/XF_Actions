[![Xamarin Mobile CI](https://github.com/danielmonettelli/XF_Actions/actions/workflows/mobile.yml/badge.svg?branch=main)](https://github.com/danielmonettelli/XF_Actions/actions/workflows/mobile.yml)

# Workflow of Xamarin.Forms

- Use of `macos-11`.
- Two jobs to build Android and iOS, which reduces waiting time.

## References of `macos-11`

[Xamarin bundles](https://github.com/actions/virtual-environments/blob/main/images/macos/macos-11-Readme.md#xamarin-bundles).

## Structure

```yml
name: Xamarin Mobile CI

on: [push, pull_request]

jobs:
  Build_Android:
    runs-on: macos-11

    steps:
      - uses: actions/checkout@v2
      - name: Set default Xamarin SDK versions
        run: |
          $VM_ASSETS/select-xamarin-sdk-v2.sh --mono=6.12 --android=11.3

      - name: Setup .NET Core SDK 5.0.x
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: "5.0.x"

      - name: Install dependencies
        run: |
          cd src
          nuget restore <sln_file_path>

      - name: Build
        run: |
          cd src
          cd XF_Actions.Android
          msbuild <csproj_file_path> /t:Rebuild /t:PackageForAndroid /p:Configuration=Debug

  Build_iOS:
    runs-on: macos-11

    steps:
      - uses: actions/checkout@v2
      - name: Set default Xamarin SDK versions
        run: |
          $VM_ASSETS/select-xamarin-sdk-v2.sh --mono=6.12 --ios=15.0

      - name: Set default Xcode 13.0
        run: |
          XCODE_ROOT=/Applications/Xcode_13.0.app
          echo "MD_APPLE_SDK_ROOT=$XCODE_ROOT" >> $GITHUB_ENV
          sudo xcode-select -s $XCODE_ROOT

      - name: Setup .NET Core SDK 5.0.x
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: "5.0.x"

      - name: Install dependencies
        run: |
          cd src
          nuget restore <sln_file_path>

      - name: Build
        run: |
          cd src
          cd XF_Actions.iOS
          msbuild <csproj_file_path> /p:Configuration=Debug /p:Platform=iPhoneSimulator /t:Rebuild
```

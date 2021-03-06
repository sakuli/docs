---
title : "Installation"
date :  2019-09-10T15:29:27+02:00
weight : 3
---

# Installation Process

The following steps are required to set up Sakuli to work with multiple browsers.
Once the initial setup is done, we will dive right into our first test.

## WebDriver Installation

Sakuli utilizes the <a href="https://www.w3.org/TR/webdriver1/" target="_blank">WebDriver protocol</a> to remote control browsers during test execution.
In addition to the browser itself, you need to install the corresponding WebDriver as well.
Several wrapper packages can be found on <a href="https://npmjs.com" target="_blank">npmjs.com</a>, which allow the installation of the required binaries via `npm`.

Since some users encountered issues with geckodriver on Firefox, we recommend using chromedriver for now. We are working on fixes and workarounds for geckodriver.

Therefore, Chrome is the preferred browser for running Sakuli tests at the moment. A suitable WebDriver can be installed via:

{{< highlight bash >}}
npm i chromedriver
{{< /highlight >}}

or

{{< highlight bash >}}
yarn add chromedriver
{{< /highlight >}}

There are also WebDriver packages for <a href="https://www.npmjs.com/package/iedriver" target="_blank">IE</a> and <a href="https://www.npmjs.com/package/edgedriver" target="_blank">Edge</a>.
macOS already ships a WebDriver for Safari, so there is no need to install an additional package.

Attention: Be careful to install the correct version of a WebDriver package according to the installed browser version. To install e.g. ChromeDriver for Chrome 73 you have to install:

{{< highlight bash >}}
npm i chromedriver@73.0.0
{{< /highlight >}}

Sakuli is not limited to work with only a single browser.
When installing multiple WebDriver packages, you can easily switch between different browsers.

### On Windows
You will have to manually add the respective WebDriver location to your systems `PATH` variable, otherwise Sakuli will not be able to find and use it. Therefore, we recommend a global driver installation on Windows.
  
{{< highlight bash >}}
npm i -g chromedriver
{{< /highlight >}}
or
{{< highlight bash >}}
yarn global add chromedriver
{{< /highlight >}}
  
Once you installed a WebDriver package via npm, you will be prompted with its installation path, so you can easily add it to your `%PATH%` variable.

**Sample path:**
{{< highlight bash >}}
%USERPROFILE%\\AppData\\Roaming\\npm\\node_modules\\chromedriver\\lib\\chromedriver\\
{{< /highlight >}}

## Sakuli Installation

### 3rd-party dependencies

One of Sakuli's core components, <a href="https://github.com/nut-tree/nut.js" target="_blank">nut.js</a>, requires OpenCV.
Sakuli ships a pre-built version of OpenCV. Nonetheless, the installation still requires some 3rd-party dependencies.

### Windows

**Note:**
We recommend using __PowerShell__ during the installation.
When using the standard terminal CMD, the installation process might lead to failures due to different behaviour.

In order to install and run Sakuli on Windows you need two additional tools: <a href="https://www.python.org/downloads/windows/" target="_blank">Python 2</a> and the <a href="https://www.microsoft.com/en-us/download/details.aspx?id=48159" target="_blank">Windows Build Tools</a>.

To avoid eventual installation problems for Windows users we recommend to first install Python 2 on your system separately by downloading the required version from the official web page. Afterwards you can install the Windows Build Tools manually or via NPM using:

{{< highlight bash >}}
npm install --global windows-build-tools
{{< /highlight >}}

or

{{< highlight bash >}}
yarn global add windows-build-tools
{{< /highlight >}}

In case of errors while installing the Windows Build Tools package via `npm`, please make sure that the PowerShell is available on your system `PATH`. Additionally, you should install the Windows Build Tools by using the PowerShell in administrative mode.
See <a href="https://github.com/felixrieseberg/windows-build-tools/issues/20#issuecomment-373885943" target="_blank">this issue</a>.

### macOS
On macOS, Xcode command line tools are required.
You can install them by running:
{{< highlight bash >}}
xcode-select --install
{{< /highlight >}}

### Linux

Depending on your distribution, Linux setups may differ.

In general, Sakuli requires:

- Python 2
- g++
- make
- libXtst
- libPng

Installation on *buntu:
{{< highlight bash >}}
sudo apt-get install build-essential python libxtst-dev libpng++-dev
{{< /highlight >}}

The installation process is an open issue and will be improved in the near future, so using Sakuli will become even more enjoyable!

### Sakuli Installation

We will now install Sakuli in our newly created project by running:

{{< highlight bash >}}
npm i @sakuli/cli
{{< /highlight >}}

or

{{< highlight bash >}}
yarn add @sakuli/cli
{{< /highlight >}}

This will install Sakuli and its required dependencies.

### Reference
- <a href="https://github.com/justadudewhohacks/opencv4nodejs#how-to-install" target="_blank">opencv4nodejs</a>
- <a href="http://robotjs.io/docs/building" target="_blank">robotjs</a>

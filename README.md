Adapter - elane.io
==================

This documentation details how to integrate elane.io Web Analytics into Intershop.

The following Intershop versions are supported out of the box.

* Intershop 7.2

## 1. Introduction

The elane.io connector adds a new service based on the standard `TrackingSFI` framework. After activating the service at the application level a JavaScript data-layer snippet is included by `<ISIncludeTrackingTool ... >` into the `DefaultPageStructure.isml` which is the basis for every displayed storefront page. Supported template names are `MainFrameHeader` and `MainFrameFooter`.

## 2. Prerequisites

Before starting with the integration the following environment must be set up.

* A valid elane.io account with a corresponding site key.
* Sufficient rights to configure the site structure in elane.io.
* An Intershop development environment.
* A functioning Intershop application server.
* An Intershop storefront project.
* The ability to deploy the storefront project onto the application server.

For the sake of simplicity the rest of this document assumes that Linux with Bash is used for Intershop development and the application server runs from `localhost`.

## 3. Obtaining the source code

The elane.io connector is available in source form which can be downloaded from the official git repository.

    # git clone http://github.com/atfogo/elane-connector-ish-7.2.git

In the local development environment the downloaded sources should be placed into the directory `$IS_SOURCE`.

## 4. Building the source code

The source code consists of a stand alone cartridge `ac_atfogo_elane`.

Minor changes to the source code could be necessary if the pipeline dictionary keys or pagelet entry point IDs differ from defaults used by _PrimeTech_.

### 4.1 Assign the cartridge to the backoffice and storefront cartridge lists

In order for the elane.io connector to work. The cartridge `ac_atfogo_elane` has to be added to the backoffice and to the storefront cartridge list.

Optionally edit the file `ac_atfogo_elane/staticfiles/cartridge/components/app-extension.component` to assign the cartridge lists as necessary. 

### 4.2. Adjust the data-layer configuration

The JavaScript data-layer snippet requires data from the `PipelineDictionary`. To extract the data a corresponding data-layer configuration should exist for every `PageletEntryPoint`. This configuration is loaded through the standard `ConfigurationMgr`.

The configuration consist of the pagelet entry point's ID followed by a list of key expression pairs. The key expression pairs are delimited by semicolons. Where the key is a new entry for the `ElaneDataLayer` and where the expression is either a literal value or an object path evaluated directly against the current `PipelineDictionary`.

For example:

    elane.dataLayer[systempage.checkout.address.pagelet2]= PageType = 'checkout'; PageIndex = #CurrentStep#

In this example configuration when the checkout address page is displayed the `ElaneDataLayer:PageType` is set to the literal value `checkout` while the `ElaneDataLayer:PageIndex`is set to the value of `CurrentStep` found in the current `PipelineDictionary`.

Optionally edit the file `ac_atfogo_elane/staticfiles/cartridge/config/elane.properties` to adjust the data-layer configuration as necessary.

### 4.3. Set up the logging

To ease customization for every `PageletEntryPoint` without a corresponding data-layer configuration a reminder is logged at `INFO` level upon each HTTP request. This may produce too much log if not every page should be tracked by elane.io.

Optionally edit the file `ac_atfogo_elane/staticfiles/share/system/config/cartridges/logback-ac_atfogo_elane.xml` to set up the logging for production as necessary.

### 4.4. Build the cartridge

Use the default `ant` task provided by Intershop to build the cartridge.

    # cd ac_atfogo_elane/build/
    # ant clean build

In the local development environment the compiled artifacts should be available in the directory `$IS_TARGET/ac_atfogo_elane/release`.

### 4.5 Assign the cartridge to the application server cartridge list

Add the cartridge `ac_atfogo_elane` to the application server's cartridge list then restart the application server.

Edit the file `$IS_SHARE/system/cartridges/cartridgelist.properties`.

## 5. Configuration of the service

After successful deployment the elane.io service has to be configured in the Intershop backoffice.

The following steps may be automatized by using `dbinit` or `dbmigrate`.

### 5.1. Operations

1. Open the _SLDSystem_ and log on to _Operations_ preferably as administrator.

2. Go to the _Sales Organizations_ then click on _PrimeTech_.

3. Select the _Services_ tab and click on _elane.io Web Analytics_.

4. Enable the service _elane.io Web Analytics_.

### 5.2. PrimeTech

1. Open the _SLDSystem_ and log on to _PrimeTech_ preferably as administrator.

2. Go to the channel _PrimeTech Specials_ and click on _Services_.

3. Under _Service Configurations_ create a new service.

4. Select _elane.io Web Analytics_. Provide a _Name_ and an _ID_ for the new service.

5. Select the _Adapter_, set the _Site Key_ and select the _Integration_ mode.

### 5.3 Manual versus automatic integration

The key difference between _Manual_ and _Automatic_ integration is, that _Automatic_ integration relies on the data-layer configuration to generate the `et.push(...)` while _Manual_ integration expects `et.push(...)` to be hard coded in every ISML page.

On should prefer adjusting the data-layer configuration as described in chapter 4.2 and using the _Automatic_ integration. 

## 6. License

Copyright (c) 2017 Atfogo GmbH.

Atfogo and elane.io are registered trademarks of Atfogo GmbH and/or its affiliates. Intershop is registered trademark of Intershop Communications AG. Other names appearing in this document may be trademarks of their respective owners.

The elane.io connector is licensed under the MIT License. See LICENSE for details.

This README document is licensed under the Creative Commons Attribution 4.0 International License. See: http://creativecommons.org/licenses/by/4.0/ for details.

---
layout: article
title: Roadmap
description: The Data Controller roadmap is aligned with the needs of our customers - we continue to build and prioritise on Features requested by, and funded by, new and existing customers.
og_image:  https://i.imgur.com/xFGhgg0.png
---

# Roadmap

## Overview

On this page you can find details of the Features that have currently been requested, that we agree would add value to the product, and are therefore in our development roadmap.

Where customers are paying for the new Features (eg with our discounted Developer Days offer), then those Features will always take priority.  Where funding is not available, new Features will be addressed during the Bench Time of our developers, and will always be performed after Bug Fixes.

If you would like to see a new Feature added to Data Controller, then let's have a chat!


## Requested Features

### Dynamic Filtering

#### Problem Statement

The existing filter box provides a list of values when selecting operators such as "IN", "=" etc.  The problem is that this dropdown does not consider existing filter selections.  So if a user filters on, say, "region", and then filters on "store", they will see stores for ALL regions (not just the region/regions already selected in the filter).

![](https://i.imgur.com/KDEVvDi.png)

#### Proposed Solution

We add a checkbox to the top right of the filter dialog (default ON) for "Dynamic Filtering".  Whilst enabled, whenever a list of values is requested, it is filtered using every filter clause EXCEPT the one currently being modified.

#### Technical Implementation

It will be necessary to first ensure that every filter clause is valid (currently, it is possible for two clauses to be invalid whilst they are being worked on).  We will need several (automated) test cases in the cypress suite:

* Simple (just two clauses)
* Multiple clauses (up to 6)
* Multiple grouped clauses
* Very large clause (so that filter query exceeds 50k characters)
* An attempt to work on several clauses in quick succession
* Both character & numeric

Currently when calling the 'public/getcolvals' service we provide a single table (iwant) with two values (libds & col).  This service needs to be extended to include an additional input (filtertable) with one column (filterline).  The filter query will be split across multiple rows in this table.

The service will need to safely validate this input query, to prevent the risk of SQL injection.  It can then be used to filter the returned output.

Additional automated SASjs tests required:

* malicious code injection
* Very large clause (so that filter query exceeds 50k characters)
* accuracy checks

### Dynamic Grid Cell Validation

#### Problem Statement

The challenge here is similar to that of [Dynamic Filtering](/roadmap/#dynamic-filtering) - when editing a value in a grid, the values presented to the user should be filtered according to additional rules, based on the values of other cells in the same row.

![](https://i.imgur.com/J1q4lqo.png)

#### Proposed Solution

Given the near infinite possibilities by which this list could be generated, the solution proposed is that provide a new config item - one that links an editable column to a FILTER_HOOK script via a web service.

In this way, the entire record can be sent to SAS, for processing by the FILTER_HOOK script, before returning the desired list of values.

This approach provides maximum flexibility for delivering bespoke values in the edit grid dropdown.

### Row Level Security

## Delivered Features

Below are some examples of Features that have been requested (and delivered) into Data Controller.

### Configurable Locale

#### Problem Statement

When importing spreadsheets with ambiguous dates (eg 01/02 or 02/01) the ANYDTDTM. informat was using the locale of the browser (en_us) instead of that of the client's actual country, resulting in incorrect dates being loaded.  This is due to the [default behaviour](https://rawsas.com/look-out-locale-gotcha/) of the SAS Stored Process server.

#### Solution

We added a [new config item](/dcc-options/#dc_locale) so that the locale can be explicitly set for all Data Controller users.
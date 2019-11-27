# Blue Button 2.0 2020 Design Challenge

This Git repository is a companion to the _Blue Button 2.0 2020 Design Challenge_ instructions
  contained in the Center for Medicare & Medicaid Services RFQ.

> Quoters will perform a lightweight design and prototyping exercise to demonstrate how Quoters might
>   deliver solutions for system and business owner needs similar to those specified in this solicitation.
> Quoters will share their prototype as source code for working software and related write ups (e.g. written responses to design prompts)
>   as well as any additional artifacts in a single GitHub repository with CMS’ GitHub account, ‘cms-oeda-1’.
> Two-factor authentication for this GitHub repository must be disabled.

## Additional Instructions

Solutions must use both sample datasets: at least one NPI-containing claims column must be mapped,
  and those mappings must include some data on the providers' name(s) from the providers dataset.

Demonstrate your mastery of the agile principles that you propose to use if you are awarded the contract.
It's worth reviewing the [Agile Manifesto](https://agilemanifesto.org/)
  and the [Twelve Principles of Agile Software](https://agilemanifesto.org/principles.html).

## Sample Datasets

This repository contains sample datasets for use in development and testing of Design Exercise solutions.

### Claims Dataset

The [sample-data/claims/](./sample-data/claims/) directory contains sample comma-separated value (CSV) files for the claims/`ExplanationOfBenefit` data that solutions should support.

Some additional notes:

* Ordering:
    * When multiple files are present, they should be sorted by name in ascending order,
        and processed in that order, one at a time.
* Format: The files are CSV files consistent with [RFC 4180](https://tools.ietf.org/html/rfc4180)
    and are UTF-8 encoded.
    * These files use pipe characters, `|`, as field separators and line breaks as record separators.
    * Note: As it itself notes, RFC 4180 isn't a standard.
      No further information about the format of these files will be provided.
      Make reasonable assumptions based on the information and data that you have, and document them.
* Rows:
    * Header row: The first row of the files contain column names,
        which correspond to data elements in the CMS Chronic Conditions Warehouse (CCW) data dictionaries, as detailed here:
        <https://www2.ccwdata.org/web/guest/data-dictionaries> (primarily the "Medicare Fee-For-Service Claims codebook).
    * Non-header rows: Other rows represent claims that should be processed.
    * Row groups: Rows are grouped by the `CLM_ID` column, with each group representing a claim.
        * The left-most columns, up through `CLM_CLNCL_TRIL_NUM`, represent the "claim header"
            and the data in these columns is expected to be the same for all rows in the group.
        * The right-most columns, from `LINE_NUM` on, represent the "claim lines",
            which detail specific services provided as part of the claim.
        * Within each group, the `LINE_NUM` column is expected to be unique.
* Columns:
    * `DML_IND`: The value of this column is expected to be either `INSERT` or `UPDATE`:
        * `INSERT`: Indicates that the claim is to be added to the data store.
        * `UPDATE`: Indicates that an existing claim with the specified `CLM_ID` in the data store is to be updated.
    * `CLM_ID`: Represents the ID of the claim, which is expected to be unique.
    * Several fields represent the ID of providers associated with the claim.
        * In particular, see the `PRF_PHYSN_NPI` and `ORG_NPI_NUM` fields.
        * These IDs were randomly assigned to the claims by the sample data generation process.
          Accordingly, they will not be associated with one another and other claim fields (e.g.
            place of service) as often as they would be in real data.
          For example, a claim might list a performing physician NPI and organizational NPI from
            completely different states, not to mention organizations.
        * Such occurences happen occassionally in real data, too, so submissions should cope with
            it.
    * As part of the sample data generation process,
        some fields have been modified to contain all `9`s or to be blank.
      Delivered solutions must cope with this,
        and with the fact that real data will not be limited in this way.

### Provider Dataset

The [sample-data/providers/](./sample-data/providers/) directory contains a dump of a PostgreSQL
  9.6 database of sample provider data that solutions should support.
The database schema includes three poorly structured tables with data about providers.
The `npi` column in each of these tables matches many of the provider IDs that can be found in the
  claims data.

Note that solutions should expect that the data in this database will change over time:
  new rows will be inserted, existing rows will be updated, and existing rows will be deleted.
No guarantees of transactional integrity will be provided; solutions should expect that data may be
  modified in counter-intuitive orders.
Solutions will only have read access to the database, so triggers and other such change-tracking
  mechanisms may not be used.

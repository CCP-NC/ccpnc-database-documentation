# Frequently Asked Questions

This page brings together common questions about depositing, searching, managing and downloading data in the **CCP-NC Database**.

For detailed step-by-step instructions, follow the links to the relevant sections of the User Guide.

---

## Depositing Data

### When should I deposit data?

Computational NMR data may be deposited while work is still in progress or after the associated research has been published.

If the work is already published, include the publication DOI in the metadata before publishing the database upload.

If the work is not yet published, you may keep the upload unpublished while preparing it, or publish it with an embargo where appropriate. Remember that **published uploads are immutable through the user interface**. If you expect to add or correct important metadata later — for example, a publication DOI that is not yet available — complete this information before final publication wherever possible.

See [Uploading Data](../user-guide/upload.md).

### Are there standard calculation parameters that my data must satisfy before they can be deposited?

No fixed computational thresholds are imposed simply to decide whether a calculation is "good enough" for inclusion.

Depositors should instead provide sufficient computational metadata for other users to understand how the calculation was performed and assess whether the result is suitable for their intended use.

### Can I deposit unpublished data?

Yes.

The database is not restricted to calculations associated with peer-reviewed publications. Unpublished calculations, thesis-related data and other scientifically useful computational datasets may also be deposited.

Where no publication DOI exists, leave the DOI field blank and use the available metadata fields, comments and references to provide sufficient context for the data.

If the data should not yet be made publicly accessible, an embargo of up to **36 months** can be selected during publication.

### Does it matter if similar calculations have already been deposited?

No. Independent calculations of similar or identical systems may still be scientifically useful, particularly where they differ in computational method, structure, functional, code version or other calculation settings.

### Can hypothetical structures be included?

Yes.

Calculations on hypothetical structures may be valuable for benchmarking, method development, structure prediction, machine-learning applications and other research purposes. Use the available metadata and notes to describe the status of the structure clearly.

### Can calculations involving a structure later found to be incorrect be included?

Yes, provided the calculation itself is a valid computational result.

Use the metadata or notes to make it clear that the structure is believed to be incorrect or superseded so that other users can interpret the record appropriately.

### I have a very large number of calculations. Can they be deposited?

Yes, subject to the current upload limits.

A single upload may currently contain up to **32 GB** of data. Large collections can be uploaded in bulk using a `metadata_info.csv` file, and ZIP or TAR archives are unpacked automatically.

For exceptionally large collections, contact the [database administrators](support.md) if you encounter practical upload or processing limits.

### Can I upload several magres files together?

Yes. This is the recommended approach for publications or other multi-record datasets.

Include all magres files together with a `metadata_info.csv` file containing one metadata row per magres file. The value in the `filename` column must match the corresponding magres filename exactly.

See [Bulk upload using `metadata_info.csv`](../user-guide/upload.md#bulk-upload-using-metadata_infocsv-recommended).

---

## Metadata and File Formats

### What file formats are supported?

The CCP-NC database inherits the wider NOMAD Oasis parser infrastructure, so file formats supported by NOMAD can be uploaded.

However, the **current CCP-NC NMR workflow is centred on magres files**, particularly magres output generated from CASTEP and Quantum ESPRESSO calculations.

Native DFT-code files may be parsed by their respective NOMAD parsers, but they currently create separate simulation/workflow entries and do **not** automatically provide the CCP-NC NMR quantities extracted from magres files.

See [Preparing your upload](../user-guide/upload.md#preparing-your-upload).

### Can I upload CASTEP or Quantum ESPRESSO output directly instead of a magres file?

You can upload native files supported by the relevant NOMAD parser, but this is not currently equivalent to depositing a CCP-NC NMR record.

At present, the CCP-NC-specific NMR workflow relies on magres data for magnetic shielding and electric field gradient quantities. Automatic linkage between native DFT workflows and magres-derived NMR records is under active development.

### What is `metadata_info.csv`?

`metadata_info.csv` is the metadata spreadsheet used for bulk magres uploads.

It contains one row per magres file and provides searchable CCP-NC metadata such as chemical name, data-distribution licence, publication DOI and external database references.

Download the template directly [here](../assets/files/metadata_info.csv), or see the [Uploading Data](../user-guide/upload.md#metadata-requirements) section for the full column reference.

### Does the metadata spreadsheet have to be named exactly `metadata_info.csv`?

Yes. For the current workflow, use the filename:

```text
metadata_info.csv
```

The `filename` value in each row must also exactly match the corresponding magres filename.

### I forgot to include `metadata_info.csv`. Has my upload failed?

Not necessarily.

Currently, absence of the metadata CSV is a **soft metadata failure rather than an automatic parsing failure**. The magres data may still parse successfully, while an ERROR-level message is recorded in the entry Logs.

For a single magres file, the parser can create an ELN metadata entry that can be completed manually.

For a bulk upload, check the Logs and correct the metadata before publication. If necessary, replace the files or metadata and reprocess the unpublished upload.

### What is `metadata.archive.json`? I did not upload that file.

For a single magres upload without `metadata_info.csv`, the CCP-NC workflow automatically creates an **Electronic Laboratory Notebook (ELN)** metadata entry named `metadata.archive.json`.

Open this entry, complete the metadata form and click **SYNC METADATA TO MAGRES ENTRY** before publishing.

See [Single magres upload using the ELN](../user-guide/upload.md#single-magres-upload-using-the-eln).

### Should I use `metadata_info.csv` or the ELN form?

Use `metadata_info.csv` for bulk uploads and, in general, when depositing a publication or collection containing several magres files.

The ELN form is a convenient alternative for a single magres file.

You may also use `metadata_info.csv` for a single file if you prefer to prepare the metadata in advance.

### How do the `extref_*` columns in `metadata_info.csv` work?

These fields describe links to external crystallographic databases.

- `extref_type` identifies the database. Accepted values are `csd`, `icsd`, `cod` or `other`.
- `extref_other` contains the lowercase name of another database when `extref_type` is `other`.
- `extref_code` contains the database identifier, such as a CSD REFCODE or an ICSD/COD numerical identifier.

See the complete metadata table in [Metadata requirements](../user-guide/upload.md#metadata-requirements).

### What metadata should I provide?

Provide as much scientifically useful metadata as possible, particularly metadata that helps other users discover and interpret the calculation.

For magres uploads this includes, where applicable:

- chemical name;
- data-distribution licence;
- publication DOI;
- structural descriptors or notes;
- external database name and reference code;
- author notes;
- author and publication references;
- dataset membership.

See [Metadata requirements](../user-guide/upload.md#metadata-requirements).

---

## Processing and Troubleshooting

### My entry says SUCCESS, but the Logs tab contains an ERROR. Is something wrong?

`SUCCESS` and the contents of the Logs describe different aspects of processing.

**SUCCESS** means that the parser completed without an uncaught failure. It does **not** guarantee that every expected metadata field was present.

For example, a magres file may parse successfully while the Logs report that `metadata_info.csv` was missing.

If a record looks incomplete or unexpected, inspect the **Logs** tab even when the processing status is SUCCESS.

### What does FAILURE mean?

`FAILURE` means that the parser could not complete processing successfully and the entry requires attention.

For an unpublished upload, correct or replace the relevant files and use the **Reprocess** control.

If failures continue, contact [Support](support.md).

### Can I reprocess or replace files?

Yes, while the upload is still unpublished.

Unpublished files can be replaced or removed, and the upload can be reprocessed. After publication, files and records become immutable through the normal user interface.

---

## Searching and Record Identifiers

### When should I use Equality search and when should I use Free-text?

For structured metadata, **Equality** is generally the most reliable search mode.

Use Free-text selectively, particularly when:

- only part of a material name is known;
- only part of a publication DOI is known; or
- you deliberately want related external-database reference codes, such as a base CSD REFCODE and its numbered variants.

For formulae, authors, DFT codes, program versions, functionals and most other structured metadata, follow the recommended Equality-search workflow in [Searching the Database](../user-guide/search.md).

### Why does my search return no results even though I think the record exists?

Check the search method and formatting first.

Common causes include:

- using Free-text where Equality is recommended;
- incorrect capitalisation for case-sensitive values;
- entering a value that does not exactly match the stored structured metadata;
- using an unpadded legacy Immutable ID;
- selecting an older NOMAD metadata path instead of the recommended field shown in the search documentation.

Autocomplete suggestions are recommended wherever they are available.

### What is the magres Immutable ID?

The **Immutable ID** is the legacy CCP-NC identifier historically associated with records in the previous magres database.

Legacy IDs are seven digits with leading zeros, for example:

```text
0002526
```

The Immutable ID is primarily a legacy identifier and should not be assumed to be assigned to newly deposited records.

### Why can an Immutable ID search return more than one record?

The legacy database supported versioning under the same magres identifier.

During migration, historical versions were imported as separate records while retaining their original Immutable ID. A small number of legacy Immutable ID searches can therefore return more than one record.

This is a migration artefact and does not indicate that the search has malfunctioned.

### Why can I search NMR parameters only for certain elements?

The search interface exposes element-resolved magnetic shielding and electric field gradient searches for the commonly used NMR elements currently configured in the CCP-NC application.

The available search fields may expand as the database and search interface continue to develop.

### Why should I use the filter panel rather than the global search bar for NMR ranges?

Numerical range filtering for site-resolved magnetic shielding and `Vzz` is currently most reliable through the NMR-parameter histograms in the filter panel.

Direct range syntax through the global search bar is not currently recommended.

---

## Datasets and Downloads

### What is the difference between Raw uploaded files and Processed data?

**Raw uploaded files** are the original files submitted with the upload.

**Processed data** are JSON representations of the parsed records organised according to the underlying NOMAD/CCP-NC data schema.

Choose Raw uploaded files when you need the original calculation files, and Processed data when you want the structured database representation.

### How do I download an entire publication or dataset?

Filter the search results by publication DOI or dataset name, select the matching records and use the **Download** control above the results table.

You can then choose between **Raw uploaded files** and **Processed data**.

See [Downloading Complete Datasets](../user-guide/results.md#downloading-complete-datasets).

### I opened a dataset and the displayed records do not look correct. What should I do?

There is a known interface issue where an individual dataset view may occasionally require a browser refresh before the correct dataset contents are displayed.

Refresh the page and verify that the dataset filter is applied.

### How should I name a dataset?

Use a descriptive dataset name.

For datasets corresponding to published work, the recommended convention is:

> Publication title (DOI)

For example:

> Higher Magnetic Fields, Finer MOF Structural Information: 17O Solid-State NMR at 35.2 T (10.1021/jacs.0c02810)

This keeps newly created datasets consistent with migrated CCP-NC collections.

---

## Publication, Licensing and Embargoes

### Can I edit a record after publishing it?

Not through the normal user interface.

Publication makes the uploaded files and entries immutable. If a published record contains an important mistake or requires administrative correction or removal, contact the [CCP-NC database administrators](support.md).

### Can I delete a published record?

Not through the normal user interface.

Contact [Support](support.md) if a published upload requires removal or administrative intervention.

### Can I publish data under an embargo?

Yes.

The upload interface currently supports embargo periods of up to **36 months**. Select the embargo period in the final **Publish** step.

Because publication makes the upload immutable, make sure the record metadata are complete before publishing under embargo.

### What licence is applied when I publish?

Currently, the underlying NOMAD publication workflow applies **CC BY 4.0** when data are published.

CCP-NC also records the depositor's intended data-distribution licence as searchable metadata. The current metadata template accepts:

- `pddl`
- `odc-by`
- `cc-by`

CCP-NC-specific publication-licence handling is still being developed.

### Why can my CCP-NC metadata licence differ from the licence shown by NOMAD?

This is a current limitation.

The CCP-NC metadata field records the depositor's intended data-distribution licence and is available for search and processed-data export. Separately, the underlying NOMAD publication workflow currently applies CC BY 4.0 at platform publication.

These two layers are being aligned as the CCP-NC service develops towards production. Users should therefore record their intended CCP-NC licence accurately in the upload metadata.

### Does the database assign a DOI to every record?

No DOI-minting service for individual CCP-NC records is currently described by the upload workflow.

Where a record is associated with a peer-reviewed publication, provide the publication DOI in its metadata.

---

## Service Status and Access

### Why does the site use ORCID Sandbox rather than my normal ORCID account?

This documentation describes the **staging** deployment.

Authentication testing therefore uses ORCID Sandbox rather than production ORCID. A Sandbox account is separate from a normal ORCID account.

See [Logging In](../getting-started.md#authentication).

### Can I search the database without logging in?

Yes.

Searching and browsing are publicly accessible. Authentication is required for actions that modify database content, such as creating uploads or managing metadata.

### Is everything described here final?

No.

The deployment accepts community feedback based on your user experience. Some interface elements, search capabilities, authentication behaviour and CCP-NC-specific workflows may change in the future.

Where a limitation is known, it is identified in the relevant documentation.

---

## Support

### What should I do if I cannot resolve a problem through the documentation?

Contact the [CCP-NC database administrators](support.md), particularly for:

- repeated parser failures;
- published records that require correction or removal;
- problems with downloads;
- account or access issues;
- behaviour that appears inconsistent with the documentation.

When reporting a processing problem, include the upload or entry identifier where possible and describe the steps that produced the issue.

# Best Practices for FAIR Management of Computed NMR Data

Computational NMR calculations can produce scientifically valuable data long after they were originally generated. Calculated properties such as magnetic shielding tensors and electric field gradients can be reused for comparison with experiment, re-analysis, benchmarking and machine-learning applications. To make such reuse meaningful, these properties should be retained together with the atomic structure on which they were calculated and sufficient computational provenance for another researcher or software tool to interpret how they were obtained.

This page describes the practices adopted by the CCP-NC database to support FAIR management of computed solid-state NMR data and provides recommendations for researchers preparing to deposit data in our database.

---

## From Magres to FAIR NMR data

The current CCP-NC database workflow is centred on the **magres file format**, a community format developed specifically for exchanging and archiving results from first-principles calculations of NMR parameters.

The magres specification was designed as both an archival and data-processing format. Rather than storing only derived values such as an isotropic shielding, a magres file can retain the underlying atomic structure and calculated NMR tensors in a precisely defined representation.

The specification separates the information into three principal areas:

- the **atomic structure**, including atomic coordinates, lattice vectors and, symmetry;
- the **calculated magnetic resonance quantities** associated with individual atomic sites such as magnetic shielding and electric field gradient or with pairs of sites such as spin-spin coupling;
- **calculation metadata** describing how the quantities were obtained.

See the [magres file-format specification](https://www.ccpnc.ac.uk/docs/magres/magres-format.pdf) for the complete definition of the format.

### Why retain the full calculated quantities?

For FAIR data management, calculated NMR data should, where possible, retain the full quantities produced by the calculation rather than only derived values used for a particular analysis.

For example, the magres specification represents magnetic shielding as a complete rank-2 tensor associated with an atomic site. Isotropic shielding can subsequently be derived from this tensor, whereas storing only the isotropic value discards information contained in the full tensor. The same principle applies to electric field gradients and indirect spin-spin couplings.

The principal quantities defined by the magres specification include:

| Quantity | Magres representation | Standard magres unit | Scientific context retained |
|---|---|---|---|
| Atomic positions | Cartesian coordinates associated with labelled atomic sites | Å | Connects calculated NMR properties to specific atoms in the structure |
| Lattice vectors | Three unit-cell vectors | Å | Defines the periodic structure associated with the calculation |
| Magnetic shielding | Full rank-2 shielding tensor for each atomic site | ppm | Preserves the calculated shielding tensor from which isotropic and tensor-derived quantities can be obtained |
| Electric field gradient | Symmetric, traceless rank-2 tensor for each atomic site | Hartree atomic units | Preserves the calculated EFG required for interpreting quadrupolar interactions |
| Indirect spin-spin coupling | Reduced coupling tensor between pairs of atomic sites | 10^19 T² J⁻¹ | Stores the isotope-independent reduced coupling from which J coupling can be obtained when nuclear gyromagnetic ratios are specified |
| Macroscopic magnetic susceptibility | Rank-2 tensor | 10⁻⁶ cm³ mol⁻¹ | Preserves the calculated bulk magnetic response where supplied |

The explicit use of units is an important part of the magres specification. Units are declared alongside the corresponding quantities so that their physical meaning does not depend on undocumented assumptions made by the software reading the file.

For indirect spin-spin coupling, magres stores the reduced coupling tensor rather than the isotope-specific J-coupling tensor. The reduced coupling is independent of the nuclear gyromagnetic ratios, allowing the corresponding J-coupling to be calculated for the required isotopes when those are specified.

!!! info "Preserve the calculation, not only the plotted result"
    Wherever possible, deposit the original magres data containing the structure and full calculated tensors rather than only selected or derived values.

### Connecting NMR quantities to atomic structure

Atomic positions and calculated NMR quantities in a magres file are linked through common site labels. CCP-NC preserves these labels during parsing, maintaining the association between each NMR quantity and its corresponding atomic site while providing traceability back to the original magres file. For periodic structures, lattice vectors are retained to preserve the unit-cell context.

### Preserve computational provenance

A calculated NMR quantity depends on both the atomic structure and the computational method used to obtain it. The magres calculation block can record provenance such as the electronic-structure code and version, pseudopotentials, plane-wave cutoff, k-point sampling and other relevant calculation parameters. The amount of information present can vary between magres files, so retaining the original calculation files and providing relevant computational metadata is recommended.

---

## A common NMR schema

A file-format specification defines how information is exchanged between programs. A searchable FAIR repository additionally requires that this information has a consistent **machine-readable meaning inside the database**.

For this reason, CCP-NC development has contributed to a dedicated [NOMAD NMR schema plugin](https://github.com/FAIRmat-NFDI/nomad-schema-plugin-nmr), which provides shared schema classes for NMR metadata.

NOMAD schemas define scientific information using named sections and quantities with defined properties such as:

- data type;
- shape;
- physical unit;
- description;
- relationships to other sections of the data model.

The CCP-NC magres workflow maps information extracted from magres files into this structured representation rather than treating the uploaded file as an opaque document. The schema explicitly defines what each quantity represents, its dimensionality and unit, and its relationship to the corresponding atom or calculation. The original magres file is retained as source data, while the parsed representation can be explored, searched and exported within the database.

---

## FAIR data infrastructure through NOMAD

The CCP-NC database is implemented as a customised **NOMAD Oasis**. NOMAD is an open-source materials-science data-management platform specifically designed to transform heterogeneous scientific files into structured, machine-actionable data.

NOMAD provides clear distinction between the **raw files** supplied by a researcher and the **processed data** generated by parsers and normalisation.

For a recognised file format, a parser extracts scientific information from the source files and maps it into the appropriate data model. Processed data follow a defined schema and can therefore be searched, visualised, queried through APIs and exported in a common representation regardless of how the information appeared in the original source file.

For the CCP-NC community, this provides two complementary levels of preservation:

1. **Original research data** — the uploaded magres and associated files remain available for download.
2. **Structured data** — scientifically relevant quantities are parsed into a common schema for search, exploration and programmatic reuse.

See the [NOMAD documentation](https://docs.nomad-lab.eu/) for further information about the underlying data-management infrastructure.

---

## How the CCP-NC Database supports FAIR principles

### Findable

A FAIR dataset should be discoverable from scientifically meaningful information rather than requiring a user to already know the filename or location of the data. The CCP-NC database indexes structured metadata that allow records to be located using properties including:

- material name and chemical composition
- elements and chemical formula
- electronic-structure code and version
- exchange-correlation functional
- author and dataset
- publication DOI
- external crystallographic database identifiers
- data-distribution licence
- site-resolved magnetic shielding ranges
- site-resolved electric field gradient ranges

NMR-specific quantities can therefore participate directly in data discovery. For example, records can be located according to the calculated shielding range of a particular element rather than only by bibliographic metadata.

Grouping related records into datasets and associating them with publications further improves discovery of complete computational studies.

!!! tip "Best practice"
Provide publication DOIs and external database identifiers where applicable, and group calculations belonging to the same publication or study into a dataset. Complete descriptive metadata make deposited calculations easier for others to discover.

---

### Accessible

Published CCP-NC records can be explored through the web interface, including their atomic structure, NMR parameters, computational provenance, associated files, datasets and publication metadata.

The original uploaded files can be downloaded for direct reuse, while processed data can be exported as structured JSON. The NOMAD infrastructure of the database also provides programmatic access to processed data through its APIs, enabling computational analysis without requiring data to be manually copied from individual web pages.

Where data should not become immediately public, the publication workflow also supports controlled collaboration and embargo before public release.

!!! tip "Best practice"
Retain and deposit the original magres files rather than only extracted tables or selected derived quantities. Before publication, check the parsed records, metadata and processing logs to ensure that the intended data are accessible and correctly represented.

---

### Interoperable

Interoperability is particularly important for computational NMR because equivalent physical quantities may originate from different electronic-structure codes and may otherwise be represented using different program-specific output formats. CCP-NC addresses this at several levels.

The magres format provides a common exchange representation for computed NMR quantities. The NMR schema provides a common machine-readable data model. The database then provides a common archive representation through which processed data can be queried independently of the original file syntax.

As a result, downstream tools do not necessarily need to understand every original electronic structure output format in order to work with the structured NMR data.

NOMAD's architecture also supports parsers for many atomistic simulation codes. The CCP-NC database workflow currently focuses on magres as the common NMR representation, while integration with the wider computational workflows is an area of continuing development.

!!! tip "Best practice"
Preserve NMR quantities using the definitions, units and conventions of the magres specification. Retain full calculated tensors where available rather than only derived scalar quantities, so that information required by other analysis tools and workflows is not discarded.

---

### Reusable

Data become reusable when another researcher can determine what they represent, their origin and under what conditions they may be used.

CCP-NC retains calculated NMR data together with their structural context, computational provenance and descriptive metadata. Publication and external database identifiers connect records to related scientific resources, while explicit data-distribution licences establish the conditions under which deposited data can be reused.

Preserving this context allows the same calculation to support uses beyond those anticipated by the original author, including comparison between computational methods, validation against experiment, statistical analysis, development of new analysis tools and construction of machine-learning datasets.

!!! tip "Best practice"
Provide sufficient structural and computational provenance for somebody outside your research group to understand how the NMR results were obtained. Record the relevant calculation method and parameters, preserve the link between NMR quantities and their atomic sites, and specify an appropriate data-distribution licence.

---

## FAIR data as a continuing process

FAIR data management extends beyond depositing files in a repository. The aim is to ensure that computed NMR data remain discoverable, interpretable and reusable by researchers and software beyond the original study for which they were generated.

The CCP-NC database provides the infrastructure to support this, while the quality and completeness of deposited data and metadata remain an important responsibility of the depositors and part of good FAIR data practice.

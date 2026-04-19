# hfx
HFX (Haplotype Frequency eXchange) Format Specification

[hfx.schema.json](hfx.schema.json) is [JSON Schema](https://json-schema.org/) for publishing Haplotype Frequency in HFX format.

## Schema Description

The file `hfx.schema.json` defines the structure of an HFX submission JSON document. At top level the document is an object with the following keys:

- `metadata`: an object describing the frequency data and provenance. Required.
- `frequencyData`: an optional array of haplotype frequency records when frequency data is inlined.

Key `metadata` properties (high-level):

- `creationDateTime` (string, date-time): timestamp when metadata was created.
- `outputResolution` (array): list of locus/resolution objects describing output resolution. Each item has `locus` (string) and `resolution` (string). HLA resolutions include values like `g`, `G`, `P`, `allele-family`, etc.; KIR uses sequence-level resolutions.
- `hfeMethod` (object): the HFE (Haplotype Frequency Estimation) method with `method` and `parameters` (array of `{parameter, value}` objects).
- `cohortDescription` (object): cohort metadata including `species`, `population` (array), `cohortSize`, and `dataSource`. Each population entry contains `name`, `geoLocation` (with `ISO3166`, optional `subdivision`, `latitude`, `longitude`), optional `populationSize` (numeric), `ethnicity`, `language`, and `religion`.
- `nomenclatureUsed` (object): `database` (one of IPD groups) and `version` string.
- `producer` (object): information about the data producer with `name`, `organisation`, `email`, and optional `publication` (`doi`, `year`).
- `frequencyLocation` (string): either a URI (e.g., `file://` or `http://`) pointing to a CSV file or the literal `inline` if `frequencyData` is included in the document.
- `frequencyFileHeader` (object, optional): maps expected field names (like `haplotype`, `frequency`, `count`) to the actual header names used in CSV files. Each property value must be a string. Example:

```json
{
	"haplotype": "Haplo",
	"frequency": "Freq",
	"count": "Count"
}
```

- `license` (object): license metadata (name, identifier, licenseDescription).
- `checkSum` (string): MD5 checksum of the referenced frequency file as a 32-character hexadecimal string.

`frequencyData` (when present) is an array of objects with the fields:

- `haplotype` (string): a GL string or haplotype identifier.
- `frequency` (number): numeric frequency.

For the exact constraints and enumerations, see the full JSON Schema in `hfx.schema.json`.

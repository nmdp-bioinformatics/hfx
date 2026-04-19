# HFX JSON Schema (Markdown)

This document is a human-friendly Markdown version of the `hfx.schema.json` JSON Schema used to validate HFX (Haplotype Frequency eXchange) submission documents.

## Overview

An HFX document is a JSON object with two top-level properties:

- `metadata` (required): an object describing provenance and structure of the frequency data.
- `frequencyData` (optional): an array of frequency records when the data is inlined.

See the full embedded schema below for exact constraints.

---

## Top-level properties

- `metadata` (object, required)
  - `creationDateTime` (string, date-time)
  - `outputResolution` (array): list of locus/resolution objects. Items reference either HLA or KIR resolution definitions.
  - `hfeMethod` (object): HFE method with `method` (string) and `parameters` (array of `{parameter, value}` objects).
  - `cohortDescription` (object): cohort metadata including `species`, `population` (array), `cohortSize` (number), and `dataSource`.
  - `nomenclatureUsed` (object): `database` and `version`.
  - `producer` (object): `name`, `organisation`, `email`, optional `publication`.
  - `frequencyLocation` (string): either a URI (e.g., `file://` or `http://`) or the literal `inline`.
  - `frequencyFileHeader` (object, optional): mapping of expected CSV field names to actual header strings.
  - `license` (object)
  - `checkSum` (string): MD5 checksum (32 hex chars).

- `frequencyData` (array, optional): inlined frequency records. Each item has:
  - `haplotype` (string): GL String
  - `frequency` (number)

## Important definitions (summary)

- `hlaResolution` — array of `{locus: string, resolution: string}` where `resolution` enum includes `g`, `G`, `P`, `allele-family`, `protein-sequence`, `coding-sequence`, `genomic-sequence`, `Serology`.
- `kirResolution` — array of `{locus, resolution}` with sequence-level resolution enum.
- `hfeMethod` — object with `method` and `parameters` (array of `{parameter, value}` strings).
- `cohort` — object describing the cohort. `population` is an array of objects each containing `name`, `geoLocation` (must include `ISO3166`, may include `subdivision`, `latitude`, `longitude`), optional `populationSize` (number), `ethnicity`, `populationDescription`, `language`, `religion`.
- `nomenclature` — `database` enum (IPD groups) and `version`.
- `producer` — required `name`, `organisation`, `email`, optional `publication` (`doi`, `year`).
- `frequencyData` — array of `{haplotype: string, frequency: number}`.
- `frequencyDataLocation` — either a `uri` string or the literal `inline`.
- `license` — object with `name`, `identifier`, `licenseDescription`.
- `frequencyFileHeader` — object mapping expected field names (e.g., `haplotype`, `frequency`, `count`) to the actual column header strings in CSV files.

## Example `frequencyFileHeader`

```json
{
  "haplotype": "Haplo",
  "frequency": "Freq",
  "count": "Count"
}
```

## Full JSON Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://phycus.org/hfx.schema.json",
  "title": "HFX Submission",
  "description": "Schema for HFX submission",
  "type": "object",
  "properties": {
    "metadata": {
      "description": "metadata corresponding to frequency file",
      "type": "object",
      "properties": {
        "creationDateTime": {
          "type": "string",
          "format": "date-time"
        },
        "outputResolution": {
          "oneOf": [
            {
              "$ref": "#/$defs/hlaResolution"
            },
            {
              "$ref": "#/$defs/kirResolution"
            }
          ]
        },
        "hfeMethod": {
          "$ref": "#/$defs/hfeMethod"
        },
        "cohortDescription": {
          "$ref": "#/$defs/cohort"
        },
        "nomenclatureUsed": {
          "$ref": "#/$defs/nomenclature"
        },
        "producer": {
          "$ref": "#/$defs/producer"
        },
        "frequencyLocation": {
          "$ref": "#/$defs/frequencyDataLocation"
        },
        "frequencyFileHeader": {
          "$ref": "#/$defs/frequencyFileHeader"
        },
        "license": {
          "$ref": "#/$defs/license"
        },
        "checkSum": {
          "type": "string",
          "pattern": "^[a-fA-F0-9]{32}$",
          "description": "MD5 checksum represented as a 32-character hexadecimal string."
        }
      },
      "required": [
        "outputResolution",
        "hfeMethod",
        "cohortDescription",
        "nomenclatureUsed",
        "frequencyLocation"
      ],
      "additionalProperties": false
    },
    "frequencyData": {
      "$ref": "#/$defs/frequencyData"
    }
  },
  "required": [
    "metadata"
  ],
  "additionalProperties": false,
  "$defs": {
    "hlaResolution": {
      "description": "an array of HLA locus and resolution pairs",
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "locus": {
            "type": "string"
          },
          "resolution": {
            "type": "string",
            "enum": [
              "G",
              "P",
              "g",
              "allele-family",
              "protein-sequence",
              "coding-sequence",
              "genomic-sequence",
              "Serology"
            ],
            "description": "set of resolutions for a given locus in frequencies"
          }
        },
        "required": [
          "locus",
          "resolution"
        ],
        "additionalProperties": false
      },
      "minItems": 1,
      "uniqueItems": false
    },
    "kirResolution": {
      "description": "an array of KIR locus and resolution pairs",
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "locus": {
            "type": "string"
          },
          "resolution": {
            "type": "string",
            "enum": [
              "protein-sequence",
              "coding-sequence",
              "genomic-sequence"
            ],
            "description": "set of resolutions for a given KIR locus in frequencies"
          }
        },
        "required": [
          "locus",
          "resolution"
        ],
        "additionalProperties": false
      }
    },
    "hfeMethod": {
      "description": "Method of HFE generation",
      "type": "object",
      "properties": {
        "method": {
          "type": "string"
        },
        "parameters": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "parameter": {
                "type": "string"
              },
              "value": {
                "type": "string"
              }
            },
            "required": [
              "parameter",
              "value"
            ],
            "additionalProperties": false
          }
        }
      },
      "required": [
        "method",
        "parameters"
      ],
      "additionalProperties": false
    },
    "cohort": {
      "description": "Description of the Cohort",
      "type": "object",
      "properties": {
        "species": {
          "type": "string",
          "description": "The species the Hf set is based on"
        },
        "population": {
          "type": "array",
          "description": "Population Details",
          "items": {
            "type": "object",
            "properties": {
              "name": {
                "type": "string",
                "description": "Short population name/code"
              },
              "geoLocation": {
                "type": "object",
                "properties": {
                  "ISO3166": {
                    "type": "string",
                    "description": "https://en.wikipedia.org/wiki/ISO_3166-2 and 3166-1"
                  },
                  "subdivision": {
                    "type": "array",
                    "items": {
                      "type": "object",
                      "properties": {
                        "name": {
                          "type": "string",
                          "description": "Name of State/Province"
                        }
                      }
                    },
                    "description": "Subdivision of or collection of subdivision of ISO_3166-1/2"
                  },
                  "latitude": {
                    "type": "string",
                    "description": "latitude degrees 2 left of decimal ISO 6709"
                  },
                  "longitude": {
                    "type": "string",
                    "description": "longitude degrees 3 less than decimal ISO 6709"
                  }
                },
                "required": [
                  "ISO3166"
                ]
              },
              "ethnicity": {
                "type": "string",
                "description": "Optional ancestry label"
              },
              "populationDescription": {
                "type": "string",
                "description": "Description of the cohort population"
              },
              "populationSize": {
                "type": "number",
                "exclusiveMinimum": 0,
                "description": "Size of this population in the sample"
              },
              "language": {
                "type": "string",
                "description": "spoken language of the population, https://www.ethnologue.com/codes/"
              },
              "religion": {
                "type": "string",
                "description": "Religion of population"
              }
            },
            "required": [
              "name",
              "geoLocation"
            ],
            "additionalProperties": true
          }
        },
        "cohortSize": {
          "type": "integer",
          "exclusiveMinimum": 0,
          "description": "Number of individuals (N) in the sample to produce frequency"
        },
        "dataSource": {
          "type": "string",
          "description": "Origin of data when generating frequency. Registry, clinic, etc.."
        }
      },
      "required": [
        "species",
        "population",
        "cohortSize"
      ],
      "additionalProperties": false
    },
    "nomenclature": {
      "description": "Nomenclature used to produce frequencies",
      "type": "object",
      "properties": {
        "database": {
          "type": "string",
          "enum": [
            "IPD-IMGT/HLA",
            "IPD-KIR",
            "IPD-MHC",
            "IPD-NHKIR"
          ],
          "description": "IPD main groups"
        },
        "group": {
          "type": "string",
          "description": "Groups such as NHP, DLA, FISH etc.."
        },
        "version": {
          "type": "string",
          "description": "Version of the IPD database"
        }
      },
      "required": [
        "database",
        "version"
      ],
      "additionalProperties": false
    },
    "producer": {
      "type": "object",
      "description": "Individual or Organization submitting the frequency data",
      "properties": {
        "name": {
          "type": "string",
          "description": "Name of Individual/Group producing the frequency data"
        },
        "organisation": {
          "type": "string",
          "description": "Name of Organization producing the frequency data"
        },
        "email": {
          "type": "string",
          "format": "email",
          "description": "Contact email for the producer"
        },
        "publication": {
          "type": "object",
          "properties": {
            "doi": {
              "type": "string",
              "description": "DOI of the publication"
            },
            "year": {
              "type": "string",
              "description": "Date of publication"
            }
          }
        }
      },
      "required": [
        "name",
        "organisation",
        "email"
      ],
      "additionalProperties": false
    },
    "frequencyData": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "haplotype": {
            "type": "string",
            "pattern": "^[gA-Z-~*:0-9\\.;>\\[\\]]*$",
            "description": "haplotype in GL String format"
          },
          "frequency": {
            "type": "number",
            "description": "Frequency of the haplotype"
          }
        },
        "required": [
          "haplotype",
          "frequency"
        ],
        "additionalProperties": false
      }
    },
    "frequencyDataLocation": {
      "oneOf": [
        {
          "type": "string",
          "format": "uri",
          "description": "URI can be http:// or file://data.csv"
        },
        {
          "type": "string",
          "enum": [
            "inline"
          ]
        }
      ],
      "description": "Frequency is either inlined or available at the given URI"
    },
    "license": {
      "type": "object",
      "properties": {
        "name": {
          "type": "string",
          "description": "Name"
        },
        "identifier": {
          "type": "string",
          "description": "SPDX identifier https://spdx.org/licenses/"
        },
        "licenseDescription": {
          "type": "string",
          "description": "Description of the license, if different from SPDX"
        }
      }
    },
    "frequencyFileHeader": {
      "description": "Optional mapping of frequency CSV header names to expected field names",
      "type": "object",
      "minProperties": 1,
      "additionalProperties": {
        "type": "string"
      }
    }
  }
}
```


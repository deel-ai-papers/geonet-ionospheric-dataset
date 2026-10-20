# GEONET-Derived Ionospheric Measurements for Earthquake Research

**Historical GNSS-derived ionospheric data and derived machine-learning products (Japan, 2010–2018)**

> **Publication status: documentation preview / dataset release pending.** This repository documents a proposed public data release. It does **not** currently establish that the approximately 80 TB of data are publicly downloadable. Storage paths, final inventory, file schemas, access instructions, and applicable third-party permissions must be verified before the release is announced as available.

## Overview

This dataset originates from research into the detection of earthquake-related ionospheric disturbances using artificial intelligence. The work was carried out within the **DEEL (DEpendable Explainable Learning)** project in a collaboration involving **Airbus**, **IRT Saint Exupéry**, and the **Institut de Physique du Globe de Paris (IPGP)**.

The research uses measurements derived from **GEONET**, Japan's dense network of approximately **1,200 permanent GNSS receiving stations**, to study variations in the ionospheric **Total Electron Content (TEC)**. In particular, **differential Vertical Total Electron Content (dVTEC)** measurements were used to investigate **co-seismic ionospheric disturbances (CIDs)** and to develop methods for detecting spatiotemporal anomalies.

The historical research database described in the source report covers **1 April 2010 through 31 December 2018**. The collection being considered for publication is approximately **80 TB** on Google Cloud Platform (GCP); its exact size and correspondence to the research database still require an inventory.

The objective of the proposed public release is to support reproducible research in **geophysics, ionospheric monitoring, GNSS data analysis, seismic-event studies, and machine learning**. These data are **not** an operational earthquake prediction or public-warning service.

## Dataset at a glance

| Property | Description |
| --- | --- |
| Proposed dataset name | GEONET-Derived Ionospheric Measurements for Earthquake Research |
| Data owner | Airbus (as stated by the proposed publisher; release authorization to be finalized) |
| Research collaboration | Airbus, IRT Saint Exupéry, IPGP |
| Research project | DEEL — DEpendable Explainable Learning |
| Geographic scope | Japan and surrounding region |
| Research database time range | 2010-04-01 to 2018-12-31 |
| Observation network | GEONET (approximately 1,200 GNSS stations) |
| Main scientific quantities | TEC, VTEC, dVTEC and related GNSS-derived measurements |
| Formats described in the research report | CSV for raw/working measurements; TFRecord for some machine-learning samples |
| Approximate proposed release size | 80 TB, **to be confirmed** |
| Current hosting | Google Cloud Platform, according to the research workflow and project information |
| Proposed publication location | Amazon S3 / Registry of Open Data on AWS, **pending acceptance** |
| Intended license | Open Data Commons Open Database License 1.0 (ODbL-1.0), **subject to rights verification and publication approval** |
| Refresh frequency | No periodic updates currently specified; confirm whether the release is a static archive |

## Scientific background

Ground-based GNSS receivers observe satellite signals that are affected by the ionosphere. Dual-frequency GNSS observations enable estimates of TEC, an integrated measure of electron density along the signal path. These measurements can be transformed into vertical TEC and studied as differential variations to highlight disturbances.

Earthquakes, tsunamis, volcanic events, and other processes can generate atmospheric waves and ionospheric perturbations. The original research focused on the detection of earthquake-related ionospheric signatures, including disturbances associated with Rayleigh waves. It examined the application of supervised and semi-supervised deep learning to sparse, time-dependent measurements.

The source report describes a substantial processing effort, on the order of **100,000 CPU hours**, to prepare an exploitable research database from GNSS observations. This is a reported historical processing estimate, **not** an independently verified cost or a specification of the proposed 80 TB release.

## Data products and formats

The scientific report describes **at least two data representations**. The precise inventory, folder structure, and proportion of each type in the proposed public release have not yet been supplied.

### 1. GNSS-derived measurements (CSV)

The original research workflow worked with daily **CSV** files covering measurements from GEONET receiving stations and GNSS satellites. The files were used in cloud-hosted extraction, transformation, filtering, and visualization pipelines.

**Expected uses:** selecting station/time subsets; inspecting ionospheric variations; reproducing dVTEC analyses and derived products.

**Important:** The exact CSV column names, data types, timestamp convention, units, quality flags, missing-value conventions, and file naming patterns have **not yet been verified**. No example schema below should be considered authoritative until a representative file has been inspected.

### 2. Processed spatiotemporal samples (TFRecord)

For machine learning, the research report describes examples serialized using TensorFlow `tf.train.Example` and stored as **TFRecord** files. A documented model input has shape:

```text
[30, 64, 64, 2]
   |   |   |  +-- channels: dVTEC measurements and measurement-availability mask
   |   +---+----- 64 x 64 spatial grid
   +------------- 30 temporal frames
```

The report describes an approximately **45-minute** sliding observation window, a **90-second** sampling interval, and geographic windows spanning **8° latitude × 8° longitude**. A mask distinguishes cells containing measurements from cells without measurements. The report also describes contextual features for some labeled samples, such as time bounds, tensor dimensions, fill factor, earthquake magnitude, and epicentral distance.

These specifications describe the **research samples**, not necessarily every file in the full proposed archive. Encoding details and feature keys should be checked against actual TFRecord objects before writing a production reader.

### Other derived products

The original work describes rendered visualizations, labeled examples, interpolated measurements, and additional preprocessing. Their presence and volumes in the 80 TB collection remain to be confirmed.

## Organization and metadata

**The actual object key layout has not yet been verified.** A published manifest should define the real paths rather than assuming an unconfirmed hierarchy. The release should include, at minimum:

- `manifest.csv` or `manifest.parquet`: object key, byte size, product type, geographic/time coverage, file format, checksum, and processing version;
- `data_dictionary.md`: field names, units, data types, null values, coordinate reference, and timestamp convention;
- `provenance.md`: source data, processing steps, responsible organizations, and version history;
- `LICENSE` and `NOTICE`: the approved license and required attribution statements;
- sample files and verified readers for each published format.

**Suggested, not actual, layout:**

```text
s3://<TO-BE-ANNOUNCED-BUCKET>/
  README.md
  metadata/
    manifest.parquet
    data_dictionary.md
    provenance.md
  measurements/
    ...
  derived/
    ...
  examples/
    ...
```

Do not use these example paths in code until the real bucket and object keys have been published.

## Accessing the dataset

### Current status

The public AWS S3 bucket name and its AWS Region have **not yet been assigned or confirmed**. The dataset is **not currently advertised as available on AWS** through this document.

When the release is accepted and uploaded, this section will be updated with the official dataset URL, S3 URI, region, directory listing instructions, any request/payment configuration, and the data manifest.

### Example: list public objects using AWS CLI (after release)

```bash
# Replace BOTH placeholders after AWS publication.
aws s3 ls s3://<TO-BE-ANNOUNCED-BUCKET>/ --recursive --no-sign-request
```

### Example: download a selected public object (after release)

```bash
aws s3 cp \
  s3://<TO-BE-ANNOUNCED-BUCKET>/<VERIFIED-OBJECT-KEY> \
  ./sample-data-file \
  --no-sign-request
```

The commands above are **templates**, not currently runnable dataset access instructions. Exact access settings may differ, and the final public release may require AWS-specific request settings.

### Example: Python/S3 access (after release)

```python
import boto3
from botocore import UNSIGNED
from botocore.config import Config

bucket = "<TO-BE-ANNOUNCED-BUCKET>"
key = "<VERIFIED-OBJECT-KEY>"

s3 = boto3.client("s3", config=Config(signature_version=UNSIGNED))
response = s3.get_object(Bucket=bucket, Key=key)
print("Object size:", response["ContentLength"], "bytes")
# Stream or parse response["Body"] using the verified file format.
```

For a large release, users should locate the needed subset via the manifest rather than attempt to download the entire collection.

## Suggested cloud analysis workflows

These are **proposed tutorials** rather than published, tested notebooks:

1. **Getting started with S3:** browse the dataset manifest and access a small time/station subset with Python and `boto3`.
2. **Ionospheric visualization:** load documented dVTEC measurements and explore known seismic-event windows, such as the March 2011 Tohoku event.
3. **Machine learning:** parse verified TFRecord schemas and train a baseline spatiotemporal anomaly classifier using AWS compute services.
4. **Optional tabular analysis:** if a validated Parquet version is released, query selected subsets using Amazon Athena or AWS Glue.

**Note:** The original report documents research implementations based on TensorFlow and a visualization application developed with Streamlit, Docker, and Google Kubernetes Engine. It does **not** establish that AWS notebooks, Athena tables, or a Parquet conversion already exist.

## Research methods and limitations

The research report documents a dataset of **10,092 balanced labeled examples** for one supervised training experiment and a separate **"Intensive" test dataset of 35,183 windows (33 GB)** for a selected period in 2015. These are **study-specific subsets**, not a breakdown of the approximately 80 TB proposed release.

The analysis addresses difficult scientific and statistical issues, including sparse observations, event-class imbalance, possible confounding ionospheric disturbances, and false alarms. The published study did **not** demonstrate a dependable operational earthquake early-warning system; researchers should consult the full report before interpreting any predictive results.

## Data quality, reproducibility and preservation

A public release should document:

- the scientific meaning and units of each measurement;
- station and satellite identifiers, temporal coverage, and coordinate conventions;
- how missing observations differ from actual zero-valued measurements;
- processing algorithms, parameters, and source software versions where available;
- splits and labels for published machine-learning subsets;
- file checksums, versioning and validation procedures.

No destructive transcoding or lossy numerical compression is proposed as the **only** archival representation. If storage optimization is performed, the publisher should record the method, retain suitable validation artifacts, and ensure that scientific values and required provenance are preserved.

## Ownership, licensing and attribution

**Airbus is identified by the intended publisher as the owner of the dataset.** Airbus intends to make an authorized release available under:

**Open Data Commons Open Database License v1.0 (ODbL-1.0)**  
SPDX identifier: [`ODbL-1.0`](https://spdx.org/licenses/ODbL-1.0.html)

The ODbL includes attribution and share-alike obligations for relevant database reuse. However, **an intention to use ODbL is not proof that all underlying rights have been cleared**. The GEONET origin of the measurements and the participation of research partners make it necessary to verify the applicable source-data licenses, contractual conditions, independent content rights, and any required approvals. The final release must carry an approved license notice and a precise attribution statement supplied by the relevant rights holders.

**Provisional attribution example — do not treat as approved legal text:**

> GEONET-derived ionospheric dataset, Airbus; research collaboration with IRT Saint Exupéry and IPGP. License intended: ODbL 1.0. See the final release's LICENSE and NOTICE files for definitive terms and source-data acknowledgments.

Until Airbus authorizes publication and the applicable upstream rights are confirmed, do not interpret this README as a grant of rights over unpublished data.

## Scientific reference

 DEEL (Dependable, Explainable & Embeddable Learning) Project, IRT Saint Exupéry. 

The report is the principal source for the description of the historical observations, processing methods, and study-specific datasets in this README. **Public URL to the report:** [add approved URL if available].

---

*This README is a preparatory publication document based on the 2022 research report and information supplied by the proposed publisher. All details of the current 80 TB archive and its final licensing, structure and availability must be validated before release.*

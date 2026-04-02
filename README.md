# Google Cloud Associate Cloud Engineer (ACE) Study Guide

**Note: These are my personal study notes that I am using to prepare myself for the Google Cloud Associate Cloud Engineer (ACE) certification exam.**

## Read the Book

The notes in this repository are compiled into a highly readable, searchable online book using **mdBook**. 

**[Read the live study guide here](https://cookieMr.github.io/gcp_ace_notes/)**

## Project Structure

The study material is organized into specific Google Cloud services and concepts. All source material is written in Markdown and located in the `src/` directory. Key areas covered include:

* **Compute Services:** Compute Engine, GKE, Cloud Run, App Engine, Cloud Functions.
* **Storage & Databases:** Cloud Storage, Cloud SQL, Cloud Spanner, BigQuery, Firestore, Bigtable, Memorystore.
* **Networking:** VPC Networks, Load Balancers, Cloud DNS, Hybrid Connectivity (Cloud VPN, Cloud Interconnect).
* **Operations & Security:** IAM, Cloud Logging, Cloud Monitoring, VPC Service Controls, Cloud Armor, Secret Manager.
* **Migration Tools:** Migrate to Virtual Machines, Database Migration Service, Storage Transfer Service.

## Building Locally

If you want to run this book locally to study offline or modify the notes, you will need the [mdBook](https://rust-lang.github.io/mdBook/) command-line tool.

1. **Install mdBook** (requires the Rust toolchain):
   ```bash
   cargo install mdbook
   ```
2. **Serve the book locally**:
   ```bash
   mdbook serve --open
   ```
   This compiles the Markdown files and opens a local web server at http://localhost:3000 with hot-reloading enabled.

## Content Generation

The core content and technical facts within these Markdown files were initially structured and generated with the assistance of AI, then curated, reviewed, and formatted specifically for this mdBook layout.

## License

This project is licensed under the [GNU General Public License v3.0 (GPLv3)](https://www.gnu.org/licenses/gpl-3.0.en.html#license-text).

You are free to use, modify, and distribute this study guide, provided that any modifications or derivative works are also distributed under the same open-source GPLv3 license. See the LICENSE file for more details.

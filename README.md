# nextstrain.org/monkeypox

This is the [Nextstrain](https://nextstrain.org) build for monkeypox virus. Output from this build is visible at [nextstrain.org/monkeypox](https://nextstrain.org/monkeypox).

## Usage

### Provision input data

Input sequences and metadata can be retrieved from data.nextstrain.org

* [sequences.fasta.xz](https://data.nextstrain.org/files/workflows/monkeypox/sequences.fasta.xz)
* [metadata.tsv.gz](https://data.nextstrain.org/files/workflows/monkeypox/metadata.tsv.gz)

Note that these data are generously shared by many labs around the world.
If you analyze and plan to publish using these data, please contact these labs first.

Within the analysis pipeline, these data are fetched from data.nextstrain.org and written to `data/` with:

```bash
nextstrain build --docker . data/sequences.fasta data/metadata.tsv
```

### Run analysis pipeline

Run pipeline to produce "overview" tree for `/monkeypox/mpxv` with:

```bash
nextstrain build --docker --cpus 1 . --configfile config/config_mpxv.yaml
```

Run pipeline to produce "outbreak" tree for `/monkeypox/hmpxv1` with:

```bash
nextstrain build --docker --cpus 1 . --configfile config/config_hmpxv1.yaml
```

Adjust the number of CPUs to what your machine has available you want to perform alignment and tree building a bit faster.

### Visualize results

View results with:

```bash
nextstrain view auspice/
```

## Configuration

Configuration takes place in `config/config.yml` by default.
The analysis pipeline is contained in `workflow/snakemake_rule/core.smk`.
This can be read top-to-bottom, each rule specifies its file inputs and output and pulls its parameters from `config`.
There is little redirection and each rule should be able to be reasoned with on its own.

### Data use

We gratefully acknowledge the authors, originating and submitting laboratories of the genetic
sequences and metadata for sharing their work. Please note that although data generators have
generously shared data in an open fashion, that does not mean there should be free license to
publish on this data. Data generators should be cited where possible and collaborations should be
sought in some circumstances. Please try to avoid scooping someone else's work. Reach out if
uncertain.

## Installation

Follow the [standard installation instructions](https://docs.nextstrain.org/en/latest/install.html) for Nextstrain's suite of software tools.
Please choose the installation method for your operating system which uses Docker, as currently the pre-release version 2 of Nextalign and Nextclade is required which we've baked into `nexstrain/base` docker image.

### Nextstrain build vs Snakemake

The above commands use the Nextstrain CLI and `nextstrain build` along with Docker to run using Nextalign v2.
Alternatively, if you [install Nextalign/Nextclade v2 locally](github.com/nextstrain/nextclade/releases) you can run the pipeline with:

```bash
snakemake -j 1 -p --configfile config/config_mpxv.yaml
snakemake -j 1 -p --configfile config/config_hmpxv1.yaml
```

But you need to call the executable `nextalign2` and `nextclade2` respectively - since that's what they are called in the docker image.

### Update colors to include new countries

Update `colors_hmpxv1.tsv` to group countries by region based on countries present in its `metadata.tsv`:

```bash
python3 scripts/update_colours.py --colors config/colors_hmpxv1.tsv \
    --metadata results/hmpxv1/metadata.tsv --output config/colors_hmpxv1.tsv
```

and similarly update `colors_mpxv.tsv`:

```bash
python3 scripts/update_colours.py --colors config/colors_mpxv.tsv \
    --metadata results/mpxv/metadata.tsv --output config/colors_mpxv.tsv
```

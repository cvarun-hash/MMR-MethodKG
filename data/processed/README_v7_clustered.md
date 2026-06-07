# MethodKG pipeline v7 clustered

This version updates the annotation-sample construction so duplicate title+abstract groups are auditable and can be sampled together.

## Key changes

- Adds `project_text_id`, a stable hash of normalized title + abstract.
- Adds `project_text_cluster_size` and `duplicate_project_text_flag` to cleaned and annotation outputs.
- Adds `--duplicate_cluster_mode`:
  - `cluster_expand` keeps all award IDs from a selected duplicate title+abstract cluster.
  - `cluster_representative` samples duplicate clusters but keeps one representative row per cluster.
  - `award` reproduces award-level sampling.
- Adds `--input_is_cleaned` so benchmark construction can start from `cleaned_nsf_awards_2000_2025.csv`.
- Writes duplicate audit reports:
  - `project_text_cluster_report.csv`
  - `annotation_project_text_cluster_report.csv`

## Recommended command from raw NSF export

```bash
python build_methodkg_pipeline_fixed_v7_clustered.py \
  --input Full_DSAA_Awards.csv \
  --outdir methodkg_outputs_v7_clustered \
  --sample_size 2500 \
  --duplicate_cluster_mode cluster_expand
```

## Recommended command from cleaned corpus

```bash
python build_methodkg_pipeline_fixed_v7_clustered.py \
  --input cleaned_nsf_awards_2000_2025.csv \
  --input_is_cleaned \
  --outdir methodkg_outputs_v7_clustered_from_cleaned \
  --sample_size 2500 \
  --duplicate_cluster_mode cluster_expand
```

## If exact 2500 rows is mandatory

`cluster_expand` preserves duplicate clusters. If preserving all duplicate rows prevents exactly 2500 rows, the sample may be slightly smaller unless `--allow_sample_size_overrun` is set. This is intentional: it avoids splitting identical title+abstract clusters.

Use this if you permit a slight overrun:

```bash
python build_methodkg_pipeline_fixed_v7_clustered.py \
  --input cleaned_nsf_awards_2000_2025.csv \
  --input_is_cleaned \
  --outdir methodkg_outputs_v7_clustered_from_cleaned \
  --sample_size 2500 \
  --duplicate_cluster_mode cluster_expand \
  --allow_sample_size_overrun
```

## v7.1 fix
This package fixes the `--input_is_cleaned` path by coercing numeric flag columns
that are reloaded from CSV as strings. This prevents string concatenation when
summing flags such as `legacy_nsf_org_flag`, and ensures `has_abstract` works
correctly for cluster-aware annotation sampling.

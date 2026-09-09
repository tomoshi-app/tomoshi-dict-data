# Tomoshi Dictionary — Open Data Layer

The open data layer of [Tomoshi](https://tomoshi.app), a Japanese dictionary
for Chinese- and English-speaking learners, developed by Y1Z (individual
developer).

This repository publishes every table of the Tomoshi dictionary database that
is derived from share-alike-licensed sources (JMdict, KANJIDIC2, Japanese
Wiktionary, KanjiVG and others), together with Tomoshi's own additions built
on top of them — including the complete Chinese gloss translations, kanji
variant usage notes, zh/ja contrast notes, English→Japanese word-choice
cards, synonym/register analysis cards and transitivity pair cards, in
Simplified Chinese, Traditional Chinese and (since the 2026-09-02 release)
English. Everything here may be reused under the terms listed below.

> 这是 Tomoshi 日语词典的开放数据层:所有由 share-alike 许可来源(JMdict 等)
> 衍生的表,连同构建于其上的中文释义、写法辨析、译法辨析、近义辨析、自他配对
> 等内容(简体、繁体,以及 2026-09-02 起的英文版),按下述许可开放提取与再分发。

## Download

The data ships as a single SQLite file, `tomoshi-dict-open.db`, attached to
each [GitHub Release](../../releases) (zstd-compressed). Releases track the
data versions shipped in the Tomoshi app.

The same file is mirrored, with identical SHA-256, on
[Hugging Face](https://huggingface.co/datasets/yuany1z/tomoshi-dict-data)
(dataset `yuany1z/tomoshi-dict-data`, tagged per version) and archived on
[Zenodo](https://doi.org/10.5281/zenodo.22672053) with a DOI for citation (this DOI always resolves
to the latest version; each release also has its own):
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.22672053.svg)](https://doi.org/10.5281/zenodo.22672053)

## What's inside

| Table | Contents |
|---|---|
| `entries`, `forms` | JMdict entries (~217k) and surface-form index. Entry JSON keeps glosses, POS, priority, JLPT level, variant notes; **example sentences are stripped** (see below) |
| `jpn_defs` | Japanese definitions: Japanese Wiktionary (via kaikki.org Wiktextract) + LLM-generated definitions derived from JMdict glosses |
| `zh_defs` / `zh_defs_zhtw` | Chinese translations of JMdict glosses (~217k, Simplified / Traditional). No `_en` variant: the English glosses are the JMdict originals inside `entries` |
| `zh_variant_note` (+`_zhtw`, `_en`) | Kanji-variant usage notes (写法辨析) |
| `cn_contrast` (+`_zhtw`) | zh/ja false-friend contrast notes (译法辨析), keyed by Chinese word |
| `cn_contrast_en` | English→Japanese word-choice cards (~9.9k): for an English word with several Japanese renderings, which one to pick and why. Keyed by English word; same schema as `cn_contrast` |
| `word_relations_analysis` (+`_zhtw`, `_en`) | Synonym/register analysis cards (近义・语体辨析) |
| `verb_pairs`, `verb_pairs_note` (+`_zhtw`, `_en`) | Transitive/intransitive verb pairs and explanation cards (自他配对) |
| `kanji`, `kanji_gloss` (+`_zhtw`) | KANJIDIC2 kanji data + Chinese kanji glosses derived from it. No `kanji_gloss_en`: English meanings are the KANJIDIC2 originals inside `kanji` |
| `kanji_strokes` | Stroke-order data from KanjiVG |
| `vocab_jlpt` | Community JLPT level estimates (not official data) |
| `freq_rank`, `kanji_words` | Frequency ranking and kanji→word mapping derived from JMdict priority markers |
| `meta`, `table_licenses` | Provenance and per-table licensing metadata |

Language suffixes: no suffix = Simplified Chinese, `_zhtw` = Traditional
Chinese, `_en` = English. The English tables keep their source names
(`cn_contrast_en`, `zh_variant_note_en`) so the schema stays identical across
languages.

Not included (proprietary or third-party layers of the app, unrelated to the
share-alike obligation): the reverse zh→ja and en→ja lookup indexes,
pitch-accent data with its human verification layer, grammar points and their
notes, conversational frequency ranking, WordNet-derived relation links, and
example sentences (Tatoeba text plus Tomoshi's Chinese translations — stripped
from the entry JSON here).

## Licensing

**This repository is not single-license.** Each table's license and required
attribution are recorded in the `table_licenses` table inside the database and
summarized in [LICENSE.md](LICENSE.md):

- Most tables: **CC BY-SA 4.0** (JMdict/KANJIDIC2 lineage, © EDRDG; Tomoshi's
  derived additions © Y1Z, same license).
- `kanji_strokes`: **CC BY-SA 3.0** (KanjiVG, © Ulrich Apel).

Full attribution notices are in [NOTICE.md](NOTICE.md). If you reuse this
data:

1. Attribute the EDRDG for JMdict/KANJIDIC2-derived content per their
   [licence statement](https://www.edrdg.org/edrdg/licence.html), and credit
   "Tomoshi (Y1Z)" for the derived Chinese-language layers;
2. License your derivative of BY-SA tables under the same (or a compatible)
   license, and state your modifications;
3. The name **"Tomoshi"** and its logo are trademarks/brand identifiers and
   are **not** licensed by this repository — don't use them in a way that
   suggests your product is Tomoshi or endorsed by it.

## Modifications statement (CC BY-SA §3(a)(1)(B))

Relative to the upstream sources, this dataset: restructures JMdict/KANJIDIC2
XML into SQLite; adds LLM-assisted derived content (Japanese definitions,
Chinese gloss translations, variant/contrast/synonym/transitivity notes in
Chinese and English, English→Japanese word-choice cards — generated with
JMdict/KANJIDIC2 gloss text as model input, then reviewed by a separate
model); derives frequency rankings from JMdict priority markers; and removes
example sentences. Generation details live in the Tomoshi app's
acknowledgements page.

## Sustainability pledge

If Tomoshi is ever permanently discontinued, the proprietary curated data
layer excluded from this repository will be re-released under a Creative
Commons license, so work built on Tomoshi data is never stranded.

## Contact

Errors in the data are best reported through the in-app one-tap report
feature; everything else: support@tomoshi.app.

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

GitHub Releases are the only channel that tracks every data version. Two older snapshots remain
available but are **no longer updated**: a mirror on
[Hugging Face](https://huggingface.co/datasets/yuany1z/tomoshi-dict-data)
(dataset `yuany1z/tomoshi-dict-data`, last updated with the 2026-10-13 release), and an archive on
[Zenodo](https://doi.org/10.5281/zenodo.22672053) with a DOI for citation (the concept DOI resolves to
the 2026-10-06 snapshot). For the current data use GitHub:
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.22672053.svg)](https://doi.org/10.5281/zenodo.22672053)

## What's inside

| Table | Contents |
|---|---|
| `entries`, `forms` | JMdict entries (~217k) and surface-form index. Entry JSON keeps glosses, POS, priority, JLPT level, variant notes; **example sentences are stripped** (see below) |
| `jpn_defs` | Japanese definitions: Japanese Wiktionary (via kaikki.org Wiktextract) + LLM-generated definitions derived from JMdict glosses |
| `zh_defs` / `zh_defs_zhtw` | Chinese translations of JMdict glosses (~217k, Simplified / Traditional); since the 2026-10-06 release each sense's `data` JSON may also carry `notes`, the Chinese translation of JMdict's English sense note (`s_inf`, 5,180 distinct notes). Since the 2026-10-13 release the glosses of the ~41k most common entries (top 40,000 by conversational frequency plus JLPT-tagged) have been reviewed sense by sense, and their Traditional glosses are translated into Taiwan usage from the English senses rather than converted from Simplified; since the 2026-11-06 release both apply to every entry with a Chinese gloss (a small number of Traditional rows still fall back to conversion). No `_en` variant: the English glosses are the JMdict originals inside `entries` (the app's English pack also overrides a few JMdict English sense notes that are wrong and not yet corrected upstream — two notes on たり as of the 2026-11-13 release; that override is not exported, so `entries` keeps the JMdict original) |
| `zh_variant_note` (+`_zhtw`, `_en`) | Kanji-variant usage notes (写法辨析) |
| `cn_contrast` (+`_zhtw`) | zh/ja false-friend contrast notes (译法辨析), keyed by Chinese word. `items` is a JSON array of `{entry_id, ja, reading, note}`; since the 2026-11-06 release an item may also carry `register` (one of `口语` / `中性` / `书面` / `正式`, the card-internal register of that candidate — a fixed Simplified enum, identical in `_zhtw`; absent means unrated), `collocation` (a short Japanese phrase using that candidate, e.g. `服を着る`, with its furigana in `collocation_tokens`: an array of `{text, reading?, word?}` segments) and, on a few hundred cards for polysemous Chinese words, `group` (a short Chinese sense label such as `穿衣` / `穿透`; Traditional in `_zhtw`); the table also gains a nullable `pitfall` column — one typical learner mistake for the card, stored without the ✗ mark (e.g. `靴を着る`), set on about 1% of cards, with furigana in `pitfall_tokens` (same segment format). In `cn_contrast_en` these columns were empty until the 2026-11-13 release, which rewrote the English cards with them filled in (`group` is an English sense label there; `register` keeps the same Simplified enum) |
| `cn_contrast_en` | English→Japanese word-choice cards (~9.9k; rewritten in the 2026-11-13 release, candidates grouped by the English word's senses): for an English word with several Japanese renderings, which one to pick and why. Keyed by English word; same schema as `cn_contrast` |
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

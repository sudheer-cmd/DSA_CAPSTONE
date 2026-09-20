# Multilingual Autocomplete & Query Suggestion Engine

A high-performance query suggestion and autocomplete engine utilizing an **Edge-Compressed Radix Tree (Patricia Trie)** with Subtree Max-Weight Top-K Pruning, Unicode script routing, and Levenshtein typo correction.

---

## Key Features

1. **Edge-Compressed Radix Tree**:
   - Collapses non-branching character sequences into single nodes, drastically reducing node count and memory footprint compared to a standard trie.
   - Dynamic $O(|w|)$ in-place edge splitting and frequency updates without index rebuilds.
2. **Subtree Max-Weight Top-K Search**:
   - Maintains cached `max_subtree_weight` on nodes.
   - Best-first branch-and-bound search terminates early once the remaining subtrees cannot beat the current $k$-th candidate score ($O(|prefix| + K \log K)$).
3. **Multilingual Support & Script Routing**:
   - Dedicated Radix Tree instances for **English (Latin)**, **Telugu (తెలుగు)**, **Tamil (தமிழ்)**, and **Hindi (हिन्दी / Devanagari)**.
   - Unicode NFKC normalization, casing, and accent-stripping routines that safely protect Indic vowel signs, viramas, and pulli marks.
   - Script-isolated routing prevents cross-script prefix pollution.
4. **Typo Correction (Edge-Batched Levenshtein DP)**:
   - Dynamic programming row evaluation directly along compressed edges.
   - Early subtree branch pruning when the minimum row edit distance exceeds $d$.
   - Damerau-Levenshtein adjacent transposition support.
5. **Rigorous Head-to-Head Benchmarking**:
   - Evaluated against **Standard Trie** (with identical Top-K caching for fair comparison) and a **Hash Baseline**.
   - Tracks Memory (RSS & `tracemalloc`), in-process and HTTP query latency percentiles (P50, P90, P95, P99), dynamic insertion throughput, and typo suggestion accuracy (MRR and Precision@K per noise bucket).
6. **REST API & Verification Demo**:
   - FastAPI server with timing headers (`X-Process-Time-Ms`).
   - Single-page vanilla HTML/JS demo (`web/index.html`).

---

## Directory Structure

```
capstone-1/
├── autocomplete_engine/
│   ├── api/
│   │   ├── __init__.py
│   │   └── server.py             # FastAPI REST endpoints
│   ├── benchmarks/
│   │   ├── __init__.py
│   │   ├── baselines.py          # Standard Trie & Hash Prefix Baselines
│   │   ├── benchmark_runner.py   # Full benchmark execution suite
│   │   └── datasets.py           # Multilingual Zipfian dataset & typo generator
│   ├── core/
│   │   ├── __init__.py
│   │   ├── engine.py             # Unified Autocomplete Engine coordinator
│   │   ├── fuzzy.py              # Edge-batched Levenshtein DP matcher
│   │   ├── node.py               # RadixNode and StandardTrieNode (__slots__)
│   │   ├── normalizer.py         # Unicode NFKC & script router
│   │   ├── persistence.py        # JSON and binary cold-start snapshotting
│   │   ├── radix_tree.py         # Compressed Trie with in-place edge split
│   │   └── standard_trie.py      # Baseline character-by-character trie
│   └── web/
│       └── index.html            # Verification UI
├── tests/
│   ├── test_benchmarks.py
│   ├── test_fairness.py          # Enforces fair baseline comparison contract
│   ├── test_fuzzy.py
│   ├── test_multilingual.py
│   └── test_radix_tree.py
├── future_work.md                # Documentation on edge re-merging & Pinyin
├── requirements.txt
└── README.md
```

---

## 🚀 Quick Start

### Windows

You don't need to manually create a virtual environment or install the dependencies.

Simply:

1. Download or clone this repository.
2. Open the project folder.
3. Double-click `run.bat`.
4. The application will automatically set up and start the server.
5. The website will open in your browser.

The application will be available at:

http://127.0.0.1:8000

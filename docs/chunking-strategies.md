# Chunking Strategies for Legal Documents

## Why Legal Documents Need Special Chunking

Legal documents have unique structure:
- **Hierarchical sections**: Article > Section > Clause > Sub-clause
- **Cross-references**: "As stated in Section 4.2(a)" must be preservable
- **Definitions sections**: Terms defined in one place, used everywhere
- **Long sentences**: Legal sentences can be 200+ words
- **Numbered lists**: Complex nested numbering (1., 1.1., (a), (i))

## Implemented Strategy: Structure-Aware Legal Chunking

### How It Works
1. **Section detection**: Regex patterns match Article, Section, Clause, Schedule, Exhibit, WHEREAS, numbered patterns
2. **Split at section boundaries**: Major sections become chunk boundaries
3. **Subdivide large sections**: By paragraphs, then sentences
4. **Preserve section path**: Each chunk carries its hierarchical position (e.g., ["Article 3", "Section 3.2", "Clause (a)"])

### Configuration
- Target chunk size: 512 tokens (~2048 characters)
- Overlap: 100 tokens (~400 characters)
- Higher overlap than typical RAG (legal cross-references need context)

### Chunk Metadata
Every chunk carries:
- `section_path`: Hierarchical position in document
- `page_numbers`: Which pages the chunk spans
- `is_definition`: Whether this is from a definitions section
- `context_prefix`: 2-3 sentence context from contextual enrichment

## Alternative: Semantic Chunking (Configurable)
- Embed each sentence, group by cosine similarity > 0.75
- Best for unstructured legal memos and opinion letters
- Not the default — structure-aware is better for contracts/regulations

## Performance
- Contract (50 pages): ~80-120 chunks
- Regulation (200 pages): ~400-600 chunks
- Processing: <1 second per document (chunking only, not including embedding)

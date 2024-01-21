# rag-insights
A collection of ideas, insights, and resources for improving RAG implementations.

## Definitions

* **Content**: Textual information presented in natural language, which may range from unstructured text to text with embedded semi-structured elements such as tables, lists, and formatted sections. This encompasses a broad spectrum of written communication forms, including but not limited to prose, lists, and tabulated data.
* **Document**: A container for content, which can be in various formats including digital (e.g., PDF files, Word documents, emails, web pages) and physical (printed materials). Documents are characterized by their capacity to encapsulate information, ranging from extensive reports to brief notes, across multiple genres and media.
* **Semantic Constituents**: The elemental components of a document's meaning, comprising identifiable entities (named, common, or abstract), their attributes, and the relationships between them. These constituents form the building blocks of the document's narrative or informational structure, representing discrete ideas, facts, arguments, or themes.
* **Semantic Structure**: The organizational framework that connects semantic constituents within a document, outlining how individual ideas, themes, or concepts are interrelated. This structure provides a blueprint for understanding the document’s comprehensive meaning, guiding how information is interpreted and related to the broader context or narrative.
* **Motif**: A pattern or theme that recurs within a single document or across multiple documents, identified by its consistent semantic structure. Motifs contribute to the overarching narrative or thematic composition of content, offering insights into prevalent ideas, trends, or concepts within or across a corpus of documents.

## Document Preprocessing Taxonomy

### 1. **Localised Explicit Content**

* **General Description**: This category includes documents where information is presented directly and unambiguously. The content is factual, straightforward, and often descriptive, requiring minimal interpretation or contextualization. The information in each section or paragraph is highly localised, typically self-contained, making it ideal for direct query matching in a RAG system.
  * **Self-contained Knowledge**
    * **Description**: These are documents where each chunk of text provides a complete and independent unit of information. The content is clear, concise, and does not require additional context for understanding. This straightforward nature makes the information easily retrievable and comprehensible.
    * **Examples**:
      * Dictionaries (specific word definitions).
      * Encyclopedias (detailed topic explanations).
      * Fact sheets (concise data or statistics).
      * FAQ sections (direct answers to common questions).
      * Instruction manuals (specific procedural instructions).
      * Product manuals (clear usage instructions).
      * Travel guides (specific location information).
      * Recipe books (step-by-step cooking instructions).
  * **Minimal Preprocessing Required**
    * **Description**: Documents in this subcategory require little to no additional processing as they present information in a straightforward and easily understandable format. The content is likely to be clear-cut, factual, and lacks ambiguity, which makes it ideal for direct retrieval without needing significant contextual analysis. The content's length can be i) inserted within an LLM's context window without impairing its ability to adequately fulfil instructions, or ii) it can be summarised to do so without suffering any information loss.
    * **Examples**:
      * News articles (summarizing specific events).
      * Product descriptions (clear features and specifications).
      * Press releases (compact company announcements).
      * Research abstracts (summarizing findings).
      * Statistical reports (with data interpretations).
      * Historical timelines (chronological event listings).

### 2. **Disjointed Explicit Content**

* **General Description**: This category encompasses documents where relevant information is spread across different sections or chunks, often lacking explicit links or references between them. This structural disjointness often results in fragmentation of information, requiring more complex preprocessing to identify and connect relevant parts.
  * **Longitudinal Semantics**
    * **Description**: In these documents, the relevant information is distributed throughout various sections, often lacking explicit connections. This disjointed nature can cause challenges in retrieving information that is contextually linked but not textually adjacent, with some sections providing background or introductory material while others contain detailed content. A deeper understanding of the document's structure is essential for effective information retrieval.
    * **Examples**:
      * Academic theses (with separate literature review and findings sections).
      * Business plans (vision in one section, strategies in another).
      * Technical whitepapers (background technology explained separately from applications).
      * Biographies (chronological life events with thematic insights in separate sections).
      * Comprehensive reports (information distributed across chapters).
      * Multi-chapter books (narrative elements dispersed throughout).
      * Long-form journalistic pieces (various aspects of a story in different sections).
      * Multi-part guides (information on a single topic split across parts).
      * Novels (plot developments across chapters).
  * **Structural Semantics**
    * **Description**: This subcategory includes documents where individual sections or paragraphs are dependent on surrounding content for full context. While each chunk may contain valuable information, its significance is heightened when considered in relation to other sections. Thus, processing needs to involve linking these chunks to their broader context within the document. While **Longitudinal Semantics** reflects a progressive, threadlike semantic dependency (i.e. theme) across the document, **Structural Semantics** resembles a semantic graph radiating from a given chunk outward towards its semantic dependencies in other chunks in the content.
    * **Examples**:
      * Research articles (methods in one section, interpretations in another).
      * Policy documents (principles in one section, applications in another).
      * Multi-chapter textbooks (concepts explained in early chapters, applied in later ones).
      * Legal documents (definitions in one part, clauses in another referencing those terms).
      * Serial blog posts (each post building on previous ones).

### 3. **Implicit Contextual Content**

* **General Description**: This category comprises documents where most chunks carry implicit information, often related to document-level metadata or overarching themes. Understanding such content requires recognizing these underlying, often unspoken, contexts.
  * **Global Metadata-Dependent**
    * **Description**: Here, the content in each section is implicitly tied to certain metadata, like the subject of the document, time frame, or specific entities. While the text may not repeatedly mention these metadata elements, they are essential for understanding the content's full scope and relevance. It is analogous to **Longitudinal Semantics**, but the semantics cascade from document-level metadata across the entire content document.
    * **Examples**:
      * Company financial reports (implicit reference to the company and financial period).
      * Product reviews (each part implicitly discussing the product).
      * Technical specifications (all sections inherently about a specific product).
      * Event summaries (each section linked to a specific event or series of events).
      * Meeting minutes (each part related to the overall meeting context).
      * Conference proceedings (each paper relating to the conference theme).
      * City council minutes (each part relating to city governance).
      * Museum exhibit guides (each section implicitly about the overall exhibit theme).
      * Travelogues (each entry tied to a specific journey or destination).
  * **Section Metadata-Dependent**
    * **Description**: This category includes documents where a specific section or subset of content carries implicit information tied to metadata relevant only to that subsection. This could be a particular aspect of a broader topic, like a department within a company, a regional focus within a global analysis, or a sub-theme within a larger thematic framework. The content may not explicitly reference this metadata in each chunk, but understanding this context is crucial for interpreting the section accurately.
    * **Examples**
      * Corporate report sections focusing specifically on a single business unit.
      * Market analysis chapters detailing trends in a specific geographical region.
      * Healthcare studies with sections exclusively dedicated to pediatric care.
      * Art exhibition catalogs with segments concentrating on a particular art genre.
      * Policy documents with parts specifically addressing environmental regulations.

### 4. **Subjective Contextual Content**

* **General Description**: This category includes documents where the information is either subjective or requires significant contextual or interpretive analysis. Understanding this content often involves reading between the lines, making sense of nuances, and inferring meanings that are not explicitly stated.
  * **Subjective Context**
    * **Description**: These documents contain information that is subjective, often based on personal opinions, experiences, or interpretations. The content may not explicitly state certain facts, requiring the reader to infer meanings or understand the context.
    * **Examples**:
      * Customer reviews (personal experiences and opinions).
      * Opinion columns (subjective viewpoints).
      * Personal blogs (individual perspectives).
      * Social media feeds (personal posts with implicit contexts).
      * Art critiques (subjective interpretations of art).
  * **Inferential Context**
    * **Description**: Content in this subcategory requires advanced processing to extract implicit meanings, emotions, and relevant insights. This involves understanding subtleties, reading into nuances, and deriving explicit knowledge from implicit or colloquial expressions.
    * **Examples**:
      * Recorded conversations (colloquial language, informal expressions).
      * Literary analyses (interpretation of themes and motifs).
      * Psychological case studies (analysis of behaviors and thoughts).
      * Political speeches (implicit messages, persuasive language).
      * Geopolitical analyses (interweaving disparate events, initiatives, and policies).
      * Cultural critiques (analysis of cultural trends and phenomena).

### 5. **Interactive or Dynamic Documents**

* **General Description**: This category includes documents that are not static but offer interactive features or change over time. This could include digital documents that allow reader interaction, or content that updates based on external data or user input, adding a layer of dynamism to the information presented.
  * **User-Interactive Documents**
    * **Description**: Documents that offer interactivity, allowing users to engage with the content in a non-linear fashion. This could include choosing what content to view or manipulate, affecting how the information is presented or understood.
    * **Examples**:
      * Interactive e-books with embedded quizzes or choices.
      * Web pages with clickable elements leading to additional information.
      * Online tutorials where users can choose learning paths.
      * Interactive data visualizations.
      * Digital museum exhibits with user-navigable content.
  * **Time-Sensitive or Evolving Documents**
    * **Description**: Documents in this subcategory change over time or are sensitive to the timing of access. This includes content that updates based on new data, external events, or ongoing contributions from multiple authors.
    * **Examples**:
      * Live-updating news feeds.
      * Wikis with community-driven content updates.
      * Market reports that evolve with new financial data.
      * Real-time sports event coverage.
      * Collaborative research documents with ongoing edits.

### 6. **Highly Specialized or Technical Documents**

* **General Description**: This category comprises documents with highly specialized or technical content, often targeted towards a specific professional audience. The complexity of the language, the specificity of the terminology, and the depth of the subject matter require a high level of expertise for full comprehension.
  * **Industry-Specific Technical Documents**
    * **Description**: These documents contain detailed technical information specific to a particular industry or field. They require specialized knowledge for interpretation and often contain jargon, technical diagrams, or data specific to that field.
    * **Examples**:
      * Medical research papers.
      * Engineering schematics and manuals.
      * Legal contracts and case law.
      * Scientific journal articles.
      * Technical standards and specifications.
  * **Professional or Academic Analysis**
    * **Description**: This subcategory includes documents that present in-depth analysis or discussion in a professional or academic context. The content is often dense, with complex arguments or theories, and is intended for a knowledgeable audience.
    * **Examples**:
      * Philosophical treatises.
      * Economic analysis reports.
      * Advanced academic textbooks.
      * Professional industry analyses.
      * Theoretical scientific papers.

### 7. **Multimedia-Integrated Content**

* **General Description**: Documents in this category are characterized by the integration of text with multimedia elements such as images, videos, or audio. The understanding of such content requires processing and interpreting both textual and non-textual components, as they often complement each other in conveying the overall message. This integration adds a layer of complexity as the context and meaning can be significantly influenced by the multimedia elements.
  * **Text and Image Synergy**
    * **Description**: Documents where text is closely intertwined with images, requiring both to be understood in conjunction. The images are not mere decorations but carry significant content that complements or elaborates on the text.
    * **Examples**:
      * Illustrated guides or textbooks.
      * Infographics and visual reports.
      * Comic books or graphic novels.
      * Photo essays in magazines.
      * Instruction manuals with detailed diagrams.
  * **Text and Audio/Video Integration**
    * **Description**: In these documents, audio or video elements are crucial for understanding the complete message. The text might provide transcriptions, descriptions, or supplementary information, but the audio/video carries essential content.
    * **Examples**:
      * Documentary film scripts with accompanying voiceovers.
      * Podcast transcripts supplemented with audio clips.
      * Online courses with video lectures and textual notes.
      * News articles with embedded video interviews.
      * Video game scripts with narrative text and gameplay footage.

## Document Preprocessing Methods

### 1. **Preprocessing Localised Explicit Content**

* **Self-contained Knowledge**
  * **Observations**:
    * No preprocessing necessary.
    * The selection of the chunk delimiter, size, and overlap size needs to be adapted to the content type (e.g. smaller for a glossary, larger for an encyclopedic entry).
    * Self contained segments tend to follow a recurrent pattern (e.g. dictionary entry, FAQ question-answer pair, etc.)
      * If recurrent pattern is easily identifiable, a handcrafted regex might suffice for chunking.
  * **Recommendations**:
    * Use `LangChain`'s `RecursiveCharacterTextSplitter()` or `MarkdownTextSplitter` for strict text format topology chunking.
    * Many of `LangChain`'s allows expanding the set of separator characters.
* **Minimal Preprocessing Required**
  * **Observations**:
    * No preprocessing necessary if the full document length fits in LLM context window without impairing its ability to adequately fulfil instructions.
  * **Recommendations**:
    * If the full document length occupies too much space in the LLM context window, summarise the content with a lossless compression rate that makes it fit the LLM context window.

### 2. **Preprocessing Disjointed Explicit Content**

* **Longitudinal Semantics**
  * **Observations**:
    * Implement "narrative stitching", by generating full-content, theme-specific summaries.
  * **Recommendations**:
* **Structural Semantics**
  * **Observations**:
  * **Recommendations**:

### 3. **Preprocessing Implicit Contextual Content**

* **Global Metadata-Dependent**
  * **Observations**:
  * **Recommendations**:
* **Section Metadata-Dependent**
  * **Observations**:
  * **Recommendations**:

### 4. **Preprocessing Subjective Contextual Content**

* **Subjective Context**
  * **Observations**:
  * **Recommendations**:
* **Inferential Context**
  * **Observations**:
  * **Recommendations**:

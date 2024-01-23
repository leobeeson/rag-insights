# rag-insights
A collection of ideas, insights, and resources for improving RAG implementations.

## Definitions

* **Retrieval Augmented Generation (RAG)**: A hybrid approach that combines the retrieval of relevant information from a dataset with the generative capabilities of large language models (LLM) to produce accurate, contextually rich responses. It enhances the depth and relevance of answers by dynamically incorporating external knowledge sources into the response generation process.
* **Content**: Textual information presented in natural language, which may range from unstructured text to text with embedded semi-structured elements such as tables, lists, and formatted sections. This encompasses a broad spectrum of written communication forms, including but not limited to prose, lists, and tabulated data.
* **Document**: A container for content, which can be in various formats including digital (e.g., PDF files, Word documents, emails, web pages) and physical (printed materials). Documents are characterized by their capacity to encapsulate information, ranging from extensive reports to brief notes, across multiple genres and media.
* **Semantic Constituents**: The elemental components of a document's meaning, comprising identifiable entities (named, common, or abstract), their attributes, and the relationships between them. These constituents form the building blocks of the document's narrative or informational structure, representing discrete ideas, facts, arguments, or themes.
* **Semantic Structure**: The organizational framework that connects semantic constituents within a document, outlining how individual ideas, themes, or concepts are interrelated. This structure provides a blueprint for understanding the document’s comprehensive meaning, guiding how information is interpreted and related to the broader context or narrative.
* **Motif**: A pattern or theme that recurs within a single document or across multiple documents, identified by its consistent semantic structure. Motifs contribute to the overarching narrative or thematic composition of content, offering insights into prevalent ideas, trends, or concepts within or across a corpus of documents.
* **Chunk**: A segment of text extracted from a document, designed to be a manageable unit for processing. In RAG systems, a chunk represents a discrete portion of content that is self-contained enough to provide contextually relevant information in response to a query. Chunks are created by segmenting documents into parts that can individually be analyzed, indexed, or retrieved, facilitating efficient information retrieval and generation. The size and scope of a chunk can vary, depending on the document's structure and the requirements of the RAG system, ranging from a single sentence to several paragraphs.

## Problems

The diversity of document formats and the complexity of content present significant challenges for `RAG` systems, including:

* **Fragmentation of Semantic Constituents**: `RAG` systems often encounter difficulties in generating comprehensive responses when relevant information is dispersed across multiple, non-sequential chunks. This fragmentation can result in the omission of critical details, as the system may not retrieve all necessary chunks due to their scattered distribution or low vector similarity to the user's query.
* **Interpretation of Implicit Content**: The challenge of discerning implicit information, whether embedded within document metadata or requiring contextualization from unretrieved chunks, poses a significant hurdle. `RAG` systems might deliver incomplete or inaccurate responses by missing out on the full context, especially when the essential subtleties that guide correct interpretation are spread across several parts of the document.
* **Subjectivity and Inference**: Dealing with subjective content or information that necessitates drawing inferences from multiple nuanced chunks presents obstacles. When not all relevant chunks are retrieved—due to their subtle relevance or low similarity scores—the system may generate responses that veer off-topic or misrepresent the intended meaning, leading to a diminished objectivity and reliability of the information provided.
* **Navigating Document Heterogeneity**: The wide range of document types, encompassing both structured and unstructured content, complicates the retrieval and preprocessing stages. Adapting to the diverse semantic structures and varying formats demands sophisticated document ETL (Extract, Transform, Load) and preprocessing techniques, critical for ensuring the relevance and precision of the information retrieved.
* **Efficiency and Scalability**: Balancing the demands of efficient processing with the need for scalability is increasingly challenging as document complexity and length escalate. `RAG` systems must manage this balance adeptly to prevent degradation in response quality or performance slowdowns, a task that grows more complex with the introduction of more intricate and voluminous documents.

Addressing these issues is crucial for refining `RAG` systems, underscoring the need for a detailed and adaptable taxonomy for document preprocessing. Such a framework is essential for improving `RAG` systems' capacity to handle a broad spectrum of document types and to navigate the intricacies of information retrieval and generation effectively.

## Document Preprocessing Taxonomy

### 1. **Localised Explicit Content**

* **General Description**: This category includes documents where information is presented directly and unambiguously. The content is factual, straightforward, and often descriptive, requiring minimal interpretation or contextualization. The information in each section or paragraph is highly localised, typically self-contained, making it ideal for direct query matching in a RAG system.
* **Subcategories**:
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
* **Subcategories**:
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
* **Subcategories**:
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
* **Subcategories**:
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
* **Subcategories**:
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
* **Subcategories**:
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
* **Subcategories**:
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

### Fix-length Character Text Splitting

* **Description**: This method divides a document's text into chunks of a specified number of characters, incorporating a set number of overlapping characters between sequential chunks to maintain context continuity.
* **Methodology**:
  * Determine the fixed number of characters per chunk (`X`).
  * Include a specified number of overlapping characters (`Y`) between sequential chunks.
  * Split the document into chunks at every `Xth` character, incorporating the `Y` overlapping characters to enhance contextual linkage without disrupting content integrity.
* **Examples**:
  * Dictionaries (specific word definitions).
  * Encyclopedias (detailed topic explanations).
  * Fact sheets (concise data or statistics).
  * FAQ sections (direct answers to common questions).
  * Instruction manuals (specific procedural instructions).
* **Recommendations**:
  * This method is particularly effective for content categories that inherently consist of discrete, self-contained units of information, which can be easily segmented into fixed-length chunks without losing their meaning or utility in a RAG system.
  * It is recommended for prototyping and development phases, but not recommended for actual production quality.
  * An analogous method is to split the text by tokens instead of characters.
  * **Resources**:
    * [LangChain: Split by Characters](https://python.langchain.com/docs/modules/data_connection/document_transformers/character_text_splitter)
    * [LangChain: Split by Tokens](https://python.langchain.com/docs/modules/data_connection/document_transformers/split_by_token)
* **Recommended Categories**:
  * **Localised Explicit Content**:
    * *Self-contained Knowledge*: Ideal for documents where each chunk can stand alone as a complete unit of information.
    * *Minimal Preprocessing Required*: Suitable for documents that are straightforward and factual, requiring little to no additional context for understanding.

### Recursive Character Text Splitting

* **Description**: This method employs a set of separator characters to divide a document into chunks, facilitating splits that are more attuned to the document's inherent logical and structural breaks.
* **Methodology**:
  * Identify key separator characters (e.g., period, newline, semicolon).
  * Optionally, include a number of overlapping characters between chunks to preserve context.
  * Recursively split the document at these separators to ensure each chunk forms a coherent unit of content.
* **Examples**:
  * Emails (where clear breaks define separate topics or sections).
  * Blog posts (structured with paragraphs encapsulating complete ideas).
  * Articles (with distinct sections or thematic divisions).
  * Instruction manuals (divided into clearly defined steps or sections).
  * FAQ sections (each Q&A as a separate unit).
* **Recommendations**:
  * This method is effective at segmenting documents while respecting their inherent logical structure.
  * **Resources**:
    * [LangChain: Recursively Split by Character](https://python.langchain.com/docs/modules/data_connection/document_transformers/recursive_text_splitter)
    * [Langchain: `RecursiveCharacterTextSplitter` API and Usage Example](https://api.python.langchain.com/en/latest/text_splitter/langchain.text_splitter.RecursiveCharacterTextSplitter.html#)
* **Recommended Categories**:
  * **Localised Explicit Content**:
    * *Self-contained Knowledge*: Perfect for documents where each paragraph or section provides a complete unit of information.
    * *Minimal Preprocessing Required*: Fits documents presenting information in a straightforward, easily understandable manner.

### Document Format-Specific Text Splitting

* **Description**: This method utilizes format or programming language-specific heuristics to segment a document into chunks, thereby maintaining the logical organization of the content along the lines of the formatting structure used to represent the content.
* **Methodology**:
  * Identify the document's format (e.g., Markdown, HTML, Python code).
  * For Markdown documents, utilize tools like [MarkdownTextSplitter](https://api.python.langchain.com/en/latest/text_splitter/langchain.text_splitter.MarkdownTextSplitter.html#langchain-text-splitter-markdowntextsplitter) to segment the content based on headers or block elements.
  * For HTML content, utilize tools like [RecursiveCharacterTextSplitter.from_language(language='html')](https://api.python.langchain.com/en/latest/text_splitter/langchain.text_splitter.RecursiveCharacterTextSplitter.html#langchain-text-splitter-recursivecharactertextsplitter), ensuring each chunk represents a coherent section or element.
  * For programming code, such as Python, use [PythonCodeTextSplitter](https://api.python.langchain.com/en/latest/text_splitter/langchain.text_splitter.PythonCodeTextSplitter.html#langchain-text-splitter-pythoncodetextsplitter) to divide the code into logical blocks, functions, or classes.
  * You can find other classes for other formats and languages in [langchain.text_splitter](https://api.python.langchain.com/en/latest/langchain_api_reference.html#module-langchain.text_splitter), along with the list of existing [programming languages](https://api.python.langchain.com/en/latest/text_splitter/langchain.text_splitter.Language.html#langchain.text_splitter.Language) that can be used for chunking.
* **Examples**:
  * Web content (HTML pages structured with clear sections and headings).
  * Programming code (Python scripts, where logical blocks can be segmented).
  * Structured documents (Markdown files, segmented by headers or lists).
  * Technical documentation (API documentation in HTML or Markdown format).
  * Online tutorials (HTML or Markdown formatted, with distinct instructional sections).
* **Recommendations**:
  * The method is adept at preserving document format integrity.
  * **Resources**:
    * [LangChain: Split by Code](https://python.langchain.com/docs/modules/data_connection/document_transformers/code_splitter)
* **Recommended Categories**:
  * **Localised Explicit Content**:
    * *Self-contained Knowledge*: Especially suited for structured documents where sections naturally form complete units of information.
  * **Disjointed Explicit Content**:
    * *Structural Semantics*: Applicable when documents have clear, format-defined sections that contribute to an overarching theme or narrative, such as unordered lists.

### Document Structure-Specific Text Splitting

* **Description**: This method leverages identifiable structural markers within documents, such as chapters, sections, subsections, page breaks, appendices, etc., to divide text into logically organized chunks that mirror the documents intrinsic structure.
* **Methodology**:
  * Utilize regular expressions to detect structural markers, identifying patterns indicative of a new chapter, section, subsection, etc.
  * Carefully segment the document at these markers to ensure that each chunk aligns with a distinct, coherent section or division, preserving the document’s structural and logical flow.
* **Examples**:
  * Academic theses (organized by chapters or sections).
  * Legal statutes (divided into clauses or articles, where each segment details specific legal codes or regulations).
  * Books (split by chapters or parts).
  * Comprehensive reports (segmented by sections or headings).
  * Multi-chapter textbooks (organized by chapters or topics, aiding in educational structuring and study).
* **Recommendations**:
  * The method is recommended particularly for documents with clearly defined sections that are meant to be consumed in a specific order or as standalone units of information.
  * Use regular expressions.
  * [Example: Breaking up text document into sections with Python using Regex match on section titles](https://stackoverflow.com/questions/48770528/breaking-up-text-document-into-sections-with-python-using-regex-match-on-section)
* **Recommended Categories**:
  * **Localised Explicit Content**:
    * *Self-contained Knowledge*: This method is particularly suited for documents where distinct sections inherently form complete units of knowledge or information, beneficial for direct retrieval and understanding.

### Parent Document Retriever

* **Description**: This method matches chunk embeddings with a user's query to retrieve the selected chunks' relevant parent documents or segments.
* **Methodology**:
  * Generate embeddings for each document chunk using a suitable NLP model such as BERT or GPT.
  * Match these embeddings with the query's embedding to determine relevance scores.
  * Retrieve and display the parent document or the most relevant segments based on matching scores.
* **Examples**:
  * Complex Legal Documents (contextual clarity from surrounding sections)
  * Scientific Research Papers (comprehensive insights from full context)
  * Technical Manuals (detailed understanding from related instructions)
  * Historical Documents (full narrative appreciation from complete records)
  * Multi-part Educational Content (cohesive learning from interconnected sections)
* **Recommendations**:
  * This method is particularly effective for complex documents where retrieving the entire content or significant segments provides the user with comprehensive context, enhancing the overall understanding and relevance of the information presented.
  * Retrieve the full parent document or section, as long as it fits within the LLM's context window.
  * Beware of burgeoning tokens costs or increased response latency given the increased prompt context size.
  * Use high-quality embeddings to improve the accuracy of relevance matching.
  * Consider pre-filtering documents based on metadata to enhance retrieval efficiency.
  * **Resources**:
    * [LangChain: Parent Document Retriever](https://python.langchain.com/docs/modules/data_connection/retrievers/parent_document_retriever)
* **Recommended Categories**:
  * **Disjointed Explicit Content**:
    * *Longitudinal Semantics*: This method fetches entire documents or significant sections that offer essential context beyond the immediate chunk, where understanding hinges on overarching themes and narratives.
    * *Structural Semantics*: This method can be used to retrieve all other chunks that share a specific metadata value for a given theme or concept that is found across different sections of the document, providing necessary context for chunks with interdependent semantics.
  * **Implicit Contextual Content**:
    * *Global Metadata-Dependent*: Retrieves comprehensive sections or entire documents encapsulating implicit themes or concepts, crucial for documents where understanding hinges on overarching metadata.
    * *Section Metadata-Dependent*: This method can be used to retrieve all other chunks that belong to the same section, where the section is tracked by a specific metadata value, providing necessary context for the segment's semantics.

### Multi-Vector Retrieval

* **Description**: Enhances chunk retrieval by assigning multiple vectors to each chunk, improving the relevance of similarity comparisons with query embeddings.
* **Methodology**:
  * Generate the primary content embedding for each chunk using an advanced NLP model.
  * Assign additional vectors for the chunk, such as from chunk summaries, parent document summaries, and thematic segment summaries to encapsulate broader semantics.
  * Use these multi-vector embeddings in similarity comparisons to enrich the retrieval process, ensuring a more nuanced match with user queries.
* **Examples**:
  * **Complex Legal Documents** (benefit from both detailed content and summary vectors for nuanced context)
  * **Scientific Research Papers** (use summary and methodological vectors for precise retrieval)
  * **Technical Documentation** (combine procedural details with overall guide summaries for targeted searches)
  * **Thematic Blog Posts** (leverage thematic summaries alongside content vectors for theme-based retrieval)
  * **Multi-Source News Articles** (utilize article summaries and thematic vectors for comprehensive context understanding)
* **Recommendations**:
  * Utilize summary vectors to capture the essence of chunks for quick relevance checks.
  * Incorporate parent document summaries to provide a macro-contextual backdrop.
  * Apply thematic segment summaries to align chunk retrieval with broader thematic inquiries.
  * **Resources**:
    * [LangChain: MultiVector Retriever](https://python.langchain.com/docs/modules/data_connection/retrievers/multi_vector)
* **Recommended Categories**:
  * **Disjointed Explicit Content**:
    * *Longitudinal Semantics*: This method is invaluable for documents with information spread out across sections, as additional vectors can bridge the context gap between non-contiguous chunks.
    * *Structural Semantics*: Enhances retrieval by providing a multi-faceted understanding of each chunk's relation to its broader structural context.
  * **Implicit Contextual Content**:
    * *Global Metadata-Dependent*: Multi-vector embeddings are critical for documents where implicit themes or concepts span across the entire content, aiding in capturing the essence of such themes.
    * *Section Metadata-Dependent*: Facilitates the nuanced retrieval of chunks that are implicitly tied to specific sub-themes or metadata within a section, by leveraging segment summaries and thematic vectors.

## Reciprocal Rank Fusion with Generated Queries

* **Description**: For every user query, pass it to an LLM to generate variations of the query, and uses all these generated queries to retrieve relevant chunks, and then rank and merges retrieved chunks.
* #TODO

## Hypothetical Document Embeddings (HyDE)

* **Description**:
  * Retrieval method used to enhance information retrieval by generating a hypothetical document for an incoming query. The process involves the following steps:
    * A Large Language Model (LLM), such as ChatGPT or InstructGPT, is fed with a query and instructed to generate a hypothetical document that answers the question. This document may contain some factual errors but is designed to mimic a relevant document.
    * An unsupervised encoder, such as Contriever, is used to encode this hypothetical document into an embedding vector. This phase involves a strategic filtration process where extraneous details are pruned to accentuate the core relevance attributes.
    * This embedding vector is then used to search against a vector database, returning the most similar real documents.
  * #TODO

## Hypothetical Chunk Questions

* **Description**: Similar to `HyDE`, but instead of creating hypothetical answers to the user query, hypothetical questions are generated for every chunk, and used to compare the incoming user query to them.
* #TODO

## Semantic Chunking Text Splitting

* **Description**: There are multiple implementations to this:
  * Clustering of Similar Sentences: Splits text into sentences, converts sentences into embeddings, compares the embeddings of successive sentences, and if very similar, will keep similar sent+0ences as part of the same chunk.
  * Clustering by Search of Break Points: Splits text into sentences, converts sentences into embeddings, aggregates every sentence embedding with its preceding and succeeding sentence embedding, and then compares the aggregate embeddings to find where one aggregate embedding has a high cosine distance (i.e. an outlier value) from the previous one, creating a break (i.e. two chunks) at said break.
  * 

## Thematic Chunking Text Splitting

**Description**: Implement "narrative stitching", by generating full-content, theme-specific clusters/summaries. There are multiple implementations to this:
  * Unsupervised Topic Chunking: 1. Creates an empty index for potential topics in the document. 2. Splits text into sentences. 3. Passes each sentence through an LLM. 4. LLM decides to which topic in the index it belongs to. 5. If it identifies a relevant topic for the sentence, it adds the sentence to that topic's chunk. 6. If no topic is relevant, create a new topic in the index, and the sentence to this new topic's chunk.
  * Supervised Topic Chunking: Same as `Unsupervised Topic Chunking`, but the developer defines the possible topics. Every sentence must be added to one of these predefined topics.
  * Semi-Supervised Topic Chunking: Same as `Supervised Topic Chunking`, but if the LLM can't assign a sentence to one of the predefined topics, it iks allowed to create new topics.
  * Narrative Chunking: Similar to `Topic Chunking`, but instead of chunking by topics, it assigns sentences to longitudinal themes (i.e. narratives) that occur throughout the document. It implies the LLM doing a first pass across the documents to identify the different overarching narratives, and then iterates over every sentence assigning each one to it;s corresponding narrative. A method could assign sentences exclusively to a single narrative, while another method could allow to assign sentences relevant to multiple narratives to each narrative to which it is relevant.
* #TODO

## Text Splitting with Metadata Enrichment

* **Description**: Irrespective of the chunking method, each chunk is enriched with chunk and document metadata.
  * Chunk Metadata Enrichment: Using an LLM for generating summaries of each chunk, extracting entities, keywords and key phrases, topics, references, headings, etc.
  * Document Metadata Enrichment: Document file metadata is embedded in each chunks metadata, such as file name, file type, creation/modification timestamps, etc. Additionally, an LLM can be used to extract global metadata (e.g. entities, keywords and key phrases, topics, references, headings, etc.) from the entire document, and embed it in each chunks metadata as global/parent metadata.
* #TODO

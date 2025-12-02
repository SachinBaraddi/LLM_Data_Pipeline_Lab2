This project demonstrates how to build a memory-efficient, true streaming data pipeline for training Large Language Models (LLMs) using the Hugging Face datasets library and PyTorch.

🎯 Goal

The primary goal of this pipeline is to process web-scale corpora without loading the entire dataset into RAM. It mimics real-world pipelines used for training large models (like GPT or Pythia) by performing on-the-fly tokenization and concatenating text into fixed-length context blocks.

🔑 Key Features

True Streaming: Uses Hugging Face's streaming=True to load data as an iterator, enabling the processing of datasets larger than available memory.

Rolling Buffer Window: Implements a logic to concatenate text chunks and group them into fixed block_size sequences (e.g., 256 tokens) across document boundaries.

PyTorch Integration: Wraps the generator in a IterableDataset and utilizes a custom collate_fn for seamless integration with PyTorch DataLoader.

Dynamic Tokenization: Tokenizes text streams in real-time using the EleutherAI/pythia-70m tokenizer.

🛠️ Prerequisites:

Python 3.10+

PyTorch

Transformers

Datasets

📦 Installation:

Install the required dependencies using pip:

!pip install transformers torch datasets

🚀 Usage:

The pipeline is contained within the provided Python script/notebook. It performs the following steps automatically:

Load Dataset: Streams the roneneldan/TinyStories dataset.

Tokenize: Maps the tokenizer over the stream without padding.

Group/Chunk: Uses a buffer to create inputs of block_size=256.

Batch: Produces PyTorch tensors ready for training.

To run the pipeline, execute the script or notebook cells sequentially.

🏗️ Pipeline Architecture:

The pipeline follows a specific flow to ensure data is continuous and shapes are consistent for the GPU.

1. The Dataset

We utilize the TinyStories dataset, which is a synthetic collection of short stories. By setting streaming=True, we avoid downloading the full dataset to the disk/RAM immediately.

2. The Rolling Buffer (Core Logic)

Standard datasets truncate text to fit a context window, often wasting data or losing context at the end of a document. This pipeline uses a Rolling Buffer approach:

Incoming text is tokenized.

Tokens are added to a buffer list.

Once len(buffer) >= block_size, a chunk is yielded.

Remaining tokens stay in the buffer for the next document.

            
3. PyTorch IterableDataset

The generator above is wrapped in a class that inherits from torch.utils.data.IterableDataset, making it compatible with the standard PyTorch training loop.

📊 Sample Output

When running the pipeline, the DataLoader produces batches with the following shape:

Batch 0 -> input_ids shape: torch.Size([8, 256])

Batch 1 -> input_ids shape: torch.Size([8, 256])

Batch 2 -> input_ids shape: torch.Size([8, 256])

Batch Size: 8

Context Length: 256 tokens

📚 Credits
Dataset: roneneldan/TinyStories

Tokenizer: EleutherAI/pythia-70m

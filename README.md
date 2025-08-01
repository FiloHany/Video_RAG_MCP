# Video RAG (Retrieval-Augmented Generation)

A powerful video retrieval and analysis system that uses the Ragie API to process, index, and query video content with natural language. This project enables semantic search through video content, extracting relevant video chunks based on text queries.

## 🎯 MCP-powered video-RAG using Ragie

This project demonstrates how to build a video-based Retrieval Augmented Generation (RAG) system powered by the Model Context Protocol (MCP). It uses Ragie's video ingestion and retrieval capabilities to enable semantic search and Q&A over video content and integrate them as MCP tools via Cursor IDE.

### Tech Stack

- **Ragie** for video ingestion + retrieval (video-RAG)
- **Cursor** as the MCP host
- **Model Context Protocol (MCP)** for AI assistant integration

## 🎯 Features

- **Video Processing**: Upload and process video files with audio-video analysis
- **Semantic Search**: Query video content using natural language
- **Video Chunking**: Extract specific video segments based on search results
- **MCP Integration**: Model Context Protocol (MCP) server for AI assistant integration
- **Jupyter Notebook Support**: Interactive development and experimentation
- **Automatic Indexing**: Clear and rebuild video indexes as needed

## 🚀 Quick Start

### Prerequisites

- Python 3.12 or higher
- Ragie API key
- Video files to process
- Cursor IDE (for MCP integration)

### Setup and Installation

#### 1. Install uv

First, let's install uv and set up our Python project and environment:

**MacOS/Linux:**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Windows:**
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

#### 2. Clone and Setup Project

```bash
# Clone the repository
git clone <your-repo-url>
cd video_rag

# Create virtual environment and activate it
uv venv
source .venv/bin/activate  # MacOS/Linux
# OR
.venv\Scripts\activate     # Windows

# Install dependencies
uv sync
```

#### 3. Configure Environment Variables

Create a `.env` file in the project root:
```env
RAGIE_API_KEY=your_ragie_api_key_here
```

#### 4. Add Your Video Files

Place your video files in the `video/` directory.

### MCP Server Setup with Cursor IDE

#### 1. Configure MCP Server in Cursor

1. Go to Cursor settings
2. Select **MCP Tools**
3. Add new global MCP server
4. In the JSON configuration, add:

```json
{
    "mcpServers": {
        "ragie": {
            "command": "uv",
            "args": [
                "--directory",
                "/absolute/path/to/project_root",
                "run",
                "server.py"
            ],
            "env": {
                "RAGIE_API_KEY": "YOUR_RAGIE_API_KEY"
            }
        }
    }
}
```

**Note:** Replace `/absolute/path/to/project_root` with the actual absolute path to your project directory.

#### 2. Connect MCP Server

1. In Cursor MCP settings, make sure to toggle the button to connect the server to the host
2. You should now see the MCP server listed in the MCP settings

#### 3. Available MCP Tools

Your custom MCP server provides 3 tools:

- **`ingest_data_tool`**: Ingests the video data to the Ragie index
- **`retrieve_data_tool`**: Retrieves relevant data from the video based on user query
- **`show_video_tool`**: Creates a short video chunk from the specified segment from the original video

You can now ingest your videos, retrieve relevant data and query it all using the Cursor Agent. The agent can even create the desired chunks from your video just with a single query!

## 🎥 Video Demonstration

Watch this video to see the Video RAG system in action:

[![Video RAG Demo](https://img.youtube.com/vi/YOUR_VIDEO_ID/0.jpg)](https://www.youtube.com/watch?v=YOUR_VIDEO_ID)

**What you'll see in the demo:**
- Setting up the MCP server with Cursor IDE
- Ingesting video content into the Ragie index
- Performing natural language queries on video content
- Extracting specific video chunks based on search results
- Real-time interaction with the Cursor Agent

*Replace `YOUR_VIDEO_ID` with your actual YouTube video ID*

## 📖 Usage

### Basic Usage

Run the main script to process videos and perform queries:

```bash
python main.py
```

This will:
1. Clear the existing index
2. Ingest all videos from the `video/` directory
3. Perform a sample query

### Interactive Development

Use the Jupyter notebook for interactive development:

```bash
jupyter notebook video_rag.ipynb
```

### MCP Server

Start the MCP server for AI assistant integration:

```bash
python server.py
```

## 🔧 API Reference

### Core Functions

#### `clear_index()`
Removes all documents from the Ragie index.

#### `ingest_data(directory: str)`
Processes and uploads all video files from the specified directory to the Ragie index.

**Parameters:**
- `directory` (str): Path to the directory containing video files

#### `retrieve_data(query: str)`
Performs semantic search on the indexed video content.

**Parameters:**
- `query` (str): Natural language query to search for in video content

**Returns:**
- List of dictionaries containing:
  - `text`: The retrieved text content
  - `document_name`: Name of the source video file
  - `start_time`: Start timestamp of the video segment
  - `end_time`: End timestamp of the video segment

#### `chunk_video(document_name: str, start_time: float, end_time: float, directory: str = "videos")`
Extracts a specific video segment and saves it as a new file.

**Parameters:**
- `document_name` (str): Name of the source video file
- `start_time` (float): Start time in seconds
- `end_time` (float): End time in seconds
- `directory` (str): Directory containing the source video (default: "videos")

**Returns:**
- Path to the created video chunk file

### MCP Tools

The project includes an MCP server with the following tools:

#### `ingest_data_tool(directory: str)`
MCP wrapper for the `ingest_data` function.

#### `retrieve_data_tool(query: str)`
MCP wrapper for the `retrieve_data` function.

#### `show_video_tool(document_name: str, start_time: float, end_time: float)`
MCP wrapper for the `chunk_video` function.

## 📝 Examples

### Example 1: Basic Video Processing and Query

```python
from main import clear_index, ingest_data, retrieve_data

# Clear existing index
clear_index()

# Ingest videos from directory
ingest_data("video")

# Query the video content
results = retrieve_data("What is the main topic of the video?")
print(results)
```

### Example 2: Extract Video Chunks

```python
from main import retrieve_data, chunk_video

# Get search results
results = retrieve_data("Show me the goal scoring moments")

# Extract video chunks for each result
for result in results:
    if result['start_time'] and result['end_time']:
        chunk_path = chunk_video(
            result['document_name'],
            result['start_time'],
            result['end_time']
        )
        print(f"Created chunk: {chunk_path}")
```

### Example 3: Jupyter Notebook Workflow

```python
# Load environment and initialize Ragie
import os
from dotenv import load_dotenv
from ragie import Ragie

load_dotenv()
ragie = Ragie(auth=os.getenv('RAGIE_API_KEY'))

# Upload a video
file_path = "video/messi-goals.mp4"
result = ragie.documents.create(request={
    "file": {
        "file_name": "messi-goals.mp4",
        "content": open(file_path, "rb"),
    },
    "mode": {
        "video": "audio_video"
    }
})

# Query the video
response = ragie.retrievals.retrieve(request={
    "query": "Give detailed description of the video with timestamp of the events"
})

# Process results
for chunk in response.scored_chunks:
    print(f"Time: {chunk.metadata.get('start_time')} - {chunk.metadata.get('end_time')}")
    print(f"Content: {chunk.text}")
    print("-" * 50)
```

## 🏗️ Project Structure

```
video_rag/
├── main.py              # Core functionality and main script
├── server.py            # MCP server implementation
├── video_rag.ipynb      # Jupyter notebook for development
├── pyproject.toml       # Project configuration and dependencies
├── README.md           # This file
├── video/              # Directory for video files
│   └── messi-goals.mp4 # Example video file
└── video_chunks/       # Output directory for video chunks (created automatically)
```

## 🔑 Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `RAGIE_API_KEY` | Your Ragie API authentication key | Yes |

## 📦 Dependencies

- **ragie**: Video processing and retrieval API
- **moviepy**: Video editing and manipulation
- **python-dotenv**: Environment variable management
- **mcp**: Model Context Protocol implementation
- **ipykernel**: Jupyter notebook support

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [Ragie](https://ragie.ai/) for providing the video processing API
- [MoviePy](https://zulko.github.io/moviepy/) for video manipulation capabilities
- [MCP](https://modelcontextprotocol.io/) for AI assistant integration

## 📞 Support

If you encounter any issues or have questions:

1. Check the [Issues](https://github.com/your-username/video-rag/issues) page
2. Create a new issue with detailed information
3. Include your Python version, error messages, and steps to reproduce

## 🔄 Changelog

### v0.1.0
- Initial release
- Basic video processing and retrieval functionality
- MCP server integration
- Jupyter notebook support
- Video chunking capabilities

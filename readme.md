# Fantasy Football Draft Agent

This project demonstrates an AI-powered fantasy football draft assistant that uses LlamaIndex and OpenAI to analyze multiple draft rankings and provide intelligent player selection recommendations.

## Overview

The agent uses a ReAct (Reasoning + Acting) framework to:
1. Index and analyze multiple fantasy football draft rankings/cheat sheets
2. Compare available players across different sources
3. Consider your current roster and draft position
4. Provide detailed reasoning for player recommendations

## Setup

### Prerequisites
- Python 3.11+
- OpenAI API key
- Fantasy football draft rankings PDFs

### Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/fantasy-football-agent.git
cd fantasy-football-agent
```

2. Set up your OpenAI API key:
```python
import os
os.environ["OPENAI_API_KEY"] = "your-api-key"
```

3. Customize by adding your own docs or changing the strategy

## How It Works


1. **Document Indexing**: 
- Creates vector store indexes of multiple draft rankings
- Enables semantic search across documents

```289:308:agent.ipynb
    "#if the indexes don't exist, create them\n",
    "if not index_loaded:\n",
    "    # load data\n",
    "    CheatSheet_1 = SimpleDirectoryReader(\n",
    "        input_files=[\"data/Fantasy-Football-2024/CheatSheet_1.pdf\"]\n",
    "    ).load_data()\n",
    "    CheatSheet_2 = SimpleDirectoryReader(\n",
    "        input_files=[\"data/Fantasy-Football-2024/CheatSheet_2.pdf\"]\n",
    "    ).load_data()\n",
    "    \n",
    "\n",
    "    # build index\n",
    "    CheatSheet_1_index = VectorStoreIndex.from_documents(CheatSheet_1)\n",
    "    CheatSheet_2_index = VectorStoreIndex.from_documents(CheatSheet_2)\n",
    "    \n",
    "\n",
    "    # persist index\n",
    "    CheatSheet_1_index.storage_context.persist(persist_dir=\"./storage/CheatSheet_1\")\n",
    "    CheatSheet_2_index.storage_context.persist(persist_dir=\"./storage/CheatSheet_2\")\n",
    "\n"
```


2. **Query Tools**:
- Position_Rankings: Analyzes player rankings by position
- Fantasy_Football_Cheatsheet: Provides detailed player analysis

```328:352:agent.ipynb
    "\n",
    "#create query engine tools\n",
    "query_engine_tools = [\n",
    "    QueryEngineTool(\n",
    "        query_engine=Cheatsheet_1_engine,\n",
    "        metadata=ToolMetadata(\n",
    "            name=\"Position_Rankings\",\n",
    "            description=(\n",
    "                \"Provides information about position rankings for each fottball position. Can be used to compare players\"\n",
    "                \"Use a detailed plain text question as input to the tool.\"\n",
    "            ),\n",
    "        ),\n",
    "    ),\n",
    "    QueryEngineTool(\n",
    "        query_engine=CheatSheet_2_engine,\n",
    "        metadata=ToolMetadata(\n",
    "            name=\"2024_Fantasy_Football_Cheatsheet\",\n",
    "            description=(\n",
    "                \"Provides detailed information about players. USe to find the best player to draft.\"\n",
    "                \"Use a detailed plain text question as input to the tool.\"\n",
    "            ),\n",
    "        ),\n",
    "    ),\n",
    "    \n",
    "]"
```


3. **Draft Strategy**:
- Incorporates predefined draft strategies
- Considers positional value and roster construction
- Adapts recommendations based on draft round

```362:404:agent.ipynb
    "context =\"\"\"You are an expert at fantasy football drafts.\n",
    " You win your league ever year and your in a draft right now.\n",
    "You need to use the tools in an intellegent way to select the best player or answer the question provided.\n",
    "Use both tools and explain your answer. Always use tools.\n",
    "\n",
    "A round, roster, and available players will be provided.\n",
    " Compare this roster to the draft strategy to determine which position is needed.\n",
    "Use the Position_Ranking Tool to identify the 3 best available player at that position.\n",
    "Then use the 2024_Fantasy_Football_Cheatsheet tool to look for the three best players.\n",
    "Finally, form a report on your findings. In the report include multiple options for players to draft.\n",
    "\n",
    "\n",
    "\n",
    "\n",
    "\n",
    "\n",
    "\n",
    "Fantasy Football Drafting Strategy\n",
    "**1. Stay Flexible:\n",
    "Be ready for unexpected twists in the draft. Have multiple strategies in mind so you can adapt on the fly.\n",
    "\n",
    "2. Focus on Positional Value:\n",
    "\n",
    "WR and RB Priority: Target WRs and RBs in the first three rounds to build a solid foundation.\n",
    "Avoid Early TE/QB: Skip drafting a TE or QB in the first three rounds, as there are usually better value options available later.\n",
    "**3. Utilize Positional Supply and Demand:\n",
    "\n",
    "Understand the Market: Consider how many players you need at each position and how many are available.\n",
    "Mispriced Assets: Identify and target players who are undervalued, allowing you to spend less on certain positions early and still get quality later.\n",
    "4. Draft Capital Allocation:\n",
    "\n",
    "Balance Your Investment: If you spend early picks on RBs, focus on WRs and Flex positions in the mid-rounds, and vice versa.\n",
    "Practice Mock Drafts: Rehearse different scenarios using mock drafts to prepare for unexpected events during the actual draft.\n",
    "5. Preferred Strategies:\n",
    "\n",
    "Hero RB: Draft one RB and two WRs in the first three rounds, then focus on WRs and TE in Rounds 4-6, and fill out RB/QB in Rounds 7-9.\n",
    "Super Hero RB: If the RB value is too good to pass up, consider taking two RBs in the first three rounds, then adjust based on how your league typically drafts.\n",
    "6. Do's and Don'ts:\n",
    "\n",
    "Do: Focus on WR and RB in the first three rounds.\n",
    "Don't: Take a TE or QB early; avoid using Heavy RB unless there's exceptional value.\n",
    "Do: Round out your roster with solid WR/RB picks in Rounds 4-6.\n",
    "Don't: Pick both a QB and TE in Rounds 3-6; diversify your selections.\"\"\"\n",
```


## Example Output

The agent provides detailed analysis including:
- Current roster evaluation
- Position needs assessment
- Multiple player recommendations with reasoning
- Comparative analysis across different rankings

## Implications for AI Agents

This project demonstrates several key capabilities of AI agents:

1. **Information Synthesis**: The agent can process and compare multiple expert sources simultaneously, something difficult for humans to do in real-time during a draft.

2. **Strategic Reasoning**: By incorporating draft strategy rules with real-time roster analysis, the agent shows how AI can handle complex, multi-factor decision making.

3. **Adaptability**: The system can be customized with different rankings and strategies, showing how AI agents can be tailored to specific needs.

4. **Transparency**: The agent provides detailed reasoning for its recommendations, making it a valuable tool for learning and decision support rather than just a black box.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Fantasy Pros for rankings data
- LlamaIndex for the indexing framework
- OpenAI for the language model

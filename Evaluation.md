Evaluation file 


Maryam Fatima
JCC



AI Agent used to assist: 
I used Gemini 31. Pro 

My Testing Prompt: 
“I'm giving you pseudocode. I need tests written for Python. 

Create tests that can verify the following, making sure there are no errors in syntax. Ask me follow up questions if any information is missing. 

[entered pseudocode]”



Test code I ended up with: 

import pytest
from agent import get_campus_resource_info

def test_library_lookup():
    result = get_campus_resource_info("library")
    assert result["status"] == "success"
    assert result["name"] == "Hultquist Library"

def test_tutoring_alias():
    result = get_campus_resource_info("tutoring")
    assert result["status"] == "success"
    assert result["name"] == "Learning Center"

def test_case_insensitive():
    result = get_campus_resource_info("LIBRARY")
    assert result["status"] == "success"
    assert result["name"] == "Hultquist Library"

def test_resource_not_found():
    result = get_campus_resource_info("dental office")
    assert result["status"] == "error"
    assert "error_message" in result
    assert "Available resources" in result["error_message"]

def test_list_all_resources():
    result = get_campus_resource_info("all")
    assert result["status"] == "success"
    assert "available_resources" in result
    assert isinstance(result["available_resources"], list)
    assert len(result["available_resources"]) >= 4


What I verified: 
The tests did not pass initially. There were 3 failures. That was on my part because I didnt give the AI my source code/ previous code. 

test summary info ===========================================

FAILED test_agent.py::test_library_lookup - AssertionError: assert 'Hultquist Library' == 'Library'

FAILED test_agent.py::test_tutoring_alias - AssertionError: assert 'Learning Center' == 'Tutoring Center'

FAILED test_agent.py::test_case_insensitive - AssertionError: assert 'Hultquist Library' == 'Library'

=================================== 3 failed, 2 passed, 1 warning in 1.20s ====================================

(base) maryam@Mac campus_agent %


I then changed the parts where the terms were different and then all 5 tests passed. 


My CI/CD workflow prompt: 
Walk me through how to write a yaml file for the following specifications: 

[Inserted the specifications/psuedocode]

The workflow I ended up with: 
1. Create the Folder Structure
This file must go in the root of your ai-programming repository, outside of the campus_agent folder.

In your VS Code Explorer (the left sidebar), click on the top-level ai-programming folder to highlight it.

Click the New Folder icon at the top of the Explorer pane and name it exactly .github (don't forget the dot at the beginning).

Right-click that new .github folder, select New Folder, and name it workflows.

Right-click the new workflows folder, select New File, and name it test.yml.

2. Paste the Workflow YAML
Copy the following YAML code and paste it into your new test.yml file. This translates your professor's pseudocode exactly into GitHub's language:

[name: Run Tests

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout the repository code
        uses: actions/checkout@v4
        
      - name: Set up Python version 3.11
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'
          
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install pytest
          
      - name: Run the tests using pytest
        run: |
          cd campus_agent
          python -m pytest test_agent.py -v]


What I changed: 
I did not change the yaml file. I was happy with it.






Reflection - answer these questions:

Q. Was the AI-generated code better or worse than what you would write? How?
The AI-generated code was alright. I would not have created all 5 tests separately. But the AI did well if we were going to be marked on 5 separate tests. I would have written something like this: 

@pytest.mark.parametrize("query, expected_name", [
    ("library", "Hultquist Library"),      # successful lookup
    ("tutoring", "Learning Center"),       # alias lookup
    ("LIBRARY", "Hultquist Library"),      # case insensitivity
])
def test_successful_lookups(query, expected_name):
    result = get_campus_resource_info(query)
    assert result["status"] == "success"
    assert result["name"] == expected_name

def test_resource_not_found():
    result = get_campus_resource_info("dental office")
    assert result["status"] == "error"
    assert "error_message" in result
    assert "Available resources" in result["error_message"]

def test_list_all_resources():
    result = get_campus_resource_info("all")
    assert result["status"] == "success"
    assert "available_resources" in result
    assert isinstance(result["available_resources"], list)
    assert len(result["available_resources"]) >= 4



Q. Did you find any "erosion" or "verbosity" (from SlopCodeBench) in the AI's output?
For the first 5 tests, I did see that it was repeating itself a bit. There is also minor architectural erosion, specifically in how we bridged the testing requirements and Streamlit's quirks. 

A few things that could be improved are: 
What I Would Do Better Next Time

Introduce Parameterization
Abstract the Async Bridge: Instead of dropping with ThreadPoolExecutor(1) as pool: directly into the chat interface block, I would write a clean, reusable helper function at the top of app.py (e.g., def run_agent_sync(prompt):). This keeps the UI code pristine and hides the messy threading mechanics.


Q. What would you do differently next time you prompt an AI for code?
I would have a set of parameters ready before I even type in what I want the code to do. This can be things like lengthiness of the code and the AI doing proof-reading on its own code and suggesting changes (not implementing them before I’ve read them though, because sometimes when I ask for improvements it will come up with improvements that arent really improvements at all, rather more complexity.)


# AI Art NFT Generation Loop with Reviewer - n8n Workflow

This repository contains an n8n workflow (`nftartn8n.json`) designed to automate the generation of AI art prompts, create images (currently via a placeholder), and facilitate a review process to iteratively improve the artwork until a desired quality score is met or a maximum number of attempts is reached.

## Workflow Overview

The workflow orchestrates the following steps:

1.  **Initialization (`init_config`):**
    *   Sets initial parameters:
        *   `base_prompt`: The starting concept for art generation.
        *   `process_history`: An array to store details of each generation attempt.
        *   `current_score`: The score of the latest generated art (starts at 0).
        *   `attempt_number`: Tracks the current attempt (starts at 1).
        *   `max_attempts`: Maximum number of attempts before stopping (default: 5).
        *   `min_score_threshold`: The minimum score required for the art to be considered satisfactory (default: 8.0).
        *   `total_api_cost`: Accumulates the cost of API calls (simulated for now).
        *   `workflow_start_time`: Records when the workflow began.

2.  **Loop Condition Check (`check_max_attempts` renamed to `Check Loop Condition`):**
    *   Checks if `attempt_number` is less than or equal to `max_attempts`.
    *   If true, proceeds to generate art.
    *   If false (max attempts reached), the workflow terminates via the `end_max_attempts` node.

3.  **Prompt Enhancement (`generate_enhanced_prompt`):**
    *   Uses OpenAI's GPT-4 Turbo API to generate a detailed, artistic prompt.
    *   The LLM is instructed to act as an expert prompt engineer, considering the `base_prompt` and `process_history` (including previous prompts, scores, and review comments) to refine and improve the prompt with each attempt.

4.  **Parse Prompt Response (`parse_prompt_response`):**
    *   Extracts the generated prompt from the OpenAI API response.
    *   Calculates the cost of the prompt generation (simulated).
    *   Updates `total_api_cost`.

5.  **Image Generation (`generate_image` - Placeholder):**
    *   **Currently a placeholder node.** It simulates calling an image generation API using the `enhanced_prompt`.
    *   Outputs a placeholder `image_url` and a simulated `image_cost`.
    *   This node needs to be replaced/configured with a real image generation API call (e.g., DALL-E, Stability AI, Midjourney if an API is available).

6.  **Manual Review (`wait_for_review`):**
    *   Pauses the workflow, presenting the `enhanced_prompt` and `image_url` (placeholder) to a human reviewer.
    *   The reviewer provides a `review_score` (0-10) and optional `review_comments`.

7.  **Set Review Score (`set_review_score`):**
    *   Updates `current_score` with the provided `review_score`.
    *   Adds the simulated `image_cost` to `total_api_cost`.
    *   Stores the `review_comments`.

8.  **Score Threshold Check (`check_score_threshold`):**
    *   Checks if `current_score` is greater than or equal to `min_score_threshold`.
    *   If true (score is satisfactory), the workflow terminates via the `end_score_met` node.
    *   If false, proceeds to the next attempt.

9.  **Increment Attempt (`increment_attempt`):**
    *   Increments `attempt_number`.
    *   Appends the details of the current attempt (prompt, score, comments) to `process_history`.
    *   Loops back to the "Check Loop Condition" node.

10. **Error Handling:**
    *   Both the OpenAI call (`generate_enhanced_prompt`) and the placeholder image generation call (`generate_image`) have retry mechanisms and error output configurations. If API calls fail after retries, error details are captured, allowing for more robust workflow execution.

## Prerequisites

*   **n8n Instance:** A running n8n instance (local, Docker, or cloud).
*   **OpenAI API Key:** Required for the prompt generation step. This should be configured as an n8n variable (e.g., `OPENAI_API_KEY`) accessible by the workflow.
*   **(Optional) Image Generation API Access:** If you replace the placeholder image generation node, you'll need API keys/access for your chosen service.

## Setup and Configuration

1.  **Import Workflow:** Import the `nftartn8n.json` file into your n8n instance.
2.  **Configure OpenAI API Key:**
    *   In n8n, set up credentials or variables for your OpenAI API key. The `generate_enhanced_prompt` node is configured to use `Bearer {{ $vars.OPENAI_API_KEY }}`. Adjust if your variable name is different.
3.  **Configure Base Prompt:**
    *   Edit the `init_config` node to set your desired `base_prompt` for art generation.
4.  **Adjust Parameters (Optional):**
    *   In the `init_config` node, you can modify `max_attempts`, `min_score_threshold`, etc., to suit your needs.
5.  **Replace Placeholder Image Generation:**
    *   The `generate_image` node is a **placeholder**. You need to replace or modify it to call a real AI image generation service.
    *   Update its URL, authentication, request body (to send the `enhanced_prompt`), and response parsing to extract the actual image URL and cost.
6.  **Activate Workflow:** Save and activate the workflow.

## Running the Workflow

*   Execute the workflow manually from the n8n editor.
*   When the `wait_for_review` node is reached, n8n will pause. Open the execution log for that node, and you will see the UI to input the score and comments.
*   Submit the review to allow the workflow to continue.

## Future Improvements

*   Implement a non-placeholder image generation step.
*   Add more sophisticated error handling branches (e.g., notifications on failure).
*   Store `process_history` in an external database for better analysis.
*   Integrate with NFT minting platforms.
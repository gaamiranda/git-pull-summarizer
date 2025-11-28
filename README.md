# Git Pull Summarizer

A Git post-merge hook that automatically generates AI-powered summaries of code changes after pulling or merging branches.

## What is post-merge?

The `post-merge` hook is a Git hook script that runs automatically after a successful `git merge` or `git pull` operation. This particular implementation uses OpenAI's API to analyze the changes and provide a concise, human-readable summary of what was updated in the codebase.

## How it works

1. **Automatic Execution**: When you run `git pull` or `git merge`, Git automatically executes the `post-merge` hook after the merge completes successfully.

2. **Change Detection**: The script runs `git diff ORIG_HEAD..HEAD` to capture all changes introduced by the merge/pull operation.

3. **Project Context**: It gathers the project structure (files up to 4 levels deep) to provide context to the AI model.

4. **AI Analysis**: The script sends the diff and project context to OpenAI's API (using the `gpt-5.1` model) with a specialized prompt that:
   - Groups changes by logical themes (e.g., API updates, refactoring, configuration)
   - Focuses on the "Why" and "How" rather than listing every file
   - Highlights potential security risks or breaking changes
   - Uses emojis for better readability

5. **Summary Display**: The AI-generated summary is displayed in your terminal, formatted with clear separators.

## Features

- ✅ Automatic execution after `git pull` or `git merge`
- ✅ Intelligent grouping of changes by theme
- ✅ Highlights breaking changes and security risks
- ✅ Skips execution if `SKIP_AI` environment variable is set
- ✅ Aborts if the diff is too large (>8000 characters) to avoid API costs

## Requirements

- Python 3
- OpenAI API key (set as `OPENAI_API_KEY` environment variable)
- Internet connection (for API calls)

## Installation

### Step 1: Copy the hook script

Copy the `post-merge` script to your repository's `.git/hooks/` directory:

```bash
cp post-merge .git/hooks/post-merge
```

### Step 2: Make it executable

The hook must be executable for Git to run it:

```bash
chmod +x .git/hooks/post-merge
```

### Step 3: Set up your OpenAI API key

Set the `OPENAI_API_KEY` environment variable. You can do this in several ways:

**Option A: Add to your shell profile** (recommended for permanent setup)
```bash
# For bash
echo 'export OPENAI_API_KEY="your-api-key-here"' >> ~/.bashrc
source ~/.bashrc

# For zsh
echo 'export OPENAI_API_KEY="your-api-key-here"' >> ~/.zshrc
source ~/.zshrc
```

**Option B: Set for current session only**
```bash
export OPENAI_API_KEY="your-api-key-here"
```

### Step 4: Test it

Try pulling or merging to see the hook in action:

```bash
git pull
```

## Usage

The hook runs automatically. No additional commands needed!

### Skipping the hook

If you want to skip the AI summarization for a specific operation, set the `SKIP_AI` environment variable:

```bash
SKIP_AI=1 git pull
```

## How it works technically

1. Git sets `ORIG_HEAD` to the previous HEAD before a merge/pull
2. After the merge, the hook compares `ORIG_HEAD` (old state) with `HEAD` (new state)
3. The diff is sent to OpenAI with project context
4. The AI returns a formatted summary that's displayed in your terminal

## Troubleshooting

**Hook not running?**
- Ensure the file is executable: `chmod +x .git/hooks/post-merge`
- Check that the file is named exactly `post-merge` (no extension)
- Verify it's in `.git/hooks/` directory

**API key error?**
- Make sure `OPENAI_API_KEY` is set: `echo $OPENAI_API_KEY`
- Verify the key is valid and has API access

**Diff too large?**
- The script automatically aborts if the diff exceeds 8000 characters
- This prevents excessive API costs for very large merges

**ORIG_HEAD missing?**
- This can happen on the first merge in a repository
- The script handles this gracefully with a warning message


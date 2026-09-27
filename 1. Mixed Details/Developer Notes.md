# Developer Notes & Reference Guide

**Author:** Mujtaba Hussain Joo  
**Last Updated:** September 20, 2026

---

## Table of Contents

1. [SSH & WSL Setup](#ssh--wsl-setup)
2. [React Installation on Ubuntu](#react-installation-on-ubuntu)
3. [React Component Syntax Patterns](#react-component-syntax-patterns)
4. [AWS AI Services](#aws-ai-services)
5. [Perplexity Copilot Extension](#perplexity-copilot-extension)
6. [API Keys & Environment Variables](#api-keys--environment-variables)
7. [Docker Commands](#docker-commands)
8. [Magento & Docker Operations](#magento--docker-operations)
9. [Linux Command Reference](#linux-command-reference)
10. [Network Configuration](#network-configuration)
11. [Trading & Market Analysis](#trading--market-analysis)
12. [Dependency Injection in PHP](#dependency-injection-in-php)
13. [Factory Pattern vs `new` in Magento](#factory-pattern-vs-new-in-magento)
14. [Credentials](#credentials)

---

## SSH & WSL Setup

### SSH Key Generation

```bash
ssh-keygen -t rsa -b 4096 -C "<your email address>"
cd ~/.ssh
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub
```

### WSL Installation & Management

```bash
wsl --install
ipconfig /flushdns
wsl --list --online
wsl --install -d <DistroName>
```

### WSL Command Reference

| Command | Purpose |
|---------|---------|
| `wsl -l -v` | List installed distros and their WSL version |
| `wsl --set-version Ubuntu 2` | Set a distro to use WSL 2 |
| `wsl.exe` | Launch your default Linux distro |
| `wsl --update` | Update WSL to the latest version |
| `wsl --status` | Check WSL status and version info |
| `wsl --shutdown` | Shut down all running WSL instances |

### Check Linux Distribution Info

```bash
lsb_release -a
```

---

## React Installation on Ubuntu

### Step 1: Update System Packages

```bash
sudo apt update -y && sudo apt upgrade -y
```

### Step 2: Install Node.js & npm

```bash
sudo apt install nodejs npm -y
```

### Step 3: Create a React App

There are two modern approaches:

#### Option A — Vite (Recommended, faster)

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm run dev
```

Your app will run at `http://localhost:5173`.

#### Option B — Create React App (classic)

```bash
npx create-react-app my-app
cd my-app
npm start
```

Your app will run at `http://localhost:3000`.

### Quick Reference

| Tool | Command | Dev Server |
|------|---------|------------|
| Vite | `npm create vite@latest my-app -- --template react` | `localhost:5173` |
| CRA | `npx create-react-app my-app` | `localhost:3000` |

> **Note:** Vite is the current industry-recommended approach as it offers significantly faster build times and hot module replacement compared to the older Create React App. For new projects, **Vite is the preferred choice**.

---

## React Component Syntax Patterns

### 1st — Function Declaration

```jsx
function Nav(props) {
    return (
        <ul>
            <li>{props.first}</li>
        </ul>
    )
}
```

### 2nd — Function Expression

```jsx
const Nav = function(props) {
    return (
        <ul>
            <li>{props.first}</li>
        </ul>
    )
}
```

### 3rd — Arrow Function with Parentheses

```jsx
const Nav = (props) => {
    return (
        <ul>
            <li>{props.first}</li>
        </ul>
    )
}
```

### 4th — Arrow Function without Parentheses (single param)

```jsx
const Nav = props => {
    return (
        <ul>
            <li>{props.first}</li>
        </ul>
    )
}
```

### 5th — Arrow Function without Props

```jsx
const Nav = () => {
    return (
        <ul>
            <li>Home</li>
        </ul>
    )
}
```

---

## AWS AI Services

- **Amazon Party Rock**
- **Amazon Bed Rock**
- **Amazon Q Dev**
- **Amazon Q Business**
- **Amazon SageMaker AI**

**Reference:** [AWS Services for AI Solutions - Coursera](https://www.coursera.org/learn/aws-services-for-ai-solutions/lecture/vTLrJ/purpose-built-ai-services-on-aws-and-their-categories)

---

## Perplexity Copilot Extension

### Installation Steps

```bash
unzip perplexity-copilot-with-icon.zip
cd pplx-copilot
npm install
npm run compile
npm install -g @vscode/vsce
vsce package --no-dependencies
code --install-extension perplexity-copilot-1.0.0.vsix
```

---

## API Keys & Environment Variables

```bash
# PageIndexkey
PageIndexkey: REDACTED_ROTATE_ME

# GROQ API Key
export GROQ_API_KEY='gsk_REDACTED_ROTATE_ME'

# OpenAI API Key
export OPENAI_API_KEY=sk-REDACTED_ROTATE_ME

# Alternative GROQ API Key
export GROQ_API_KEY=gsk_REDACTED_ROTATE_ME

# Additional Key
AQ.AbREDACTED_ROTATE_ME
```

---

## Docker Commands

### Docker Hub Operations

```bash
# Login to Docker Hub
docker login

# Tag your image
docker tag my-image:latest yourusername/my-image:latest

# Push to Docker Hub
docker push yourusername/my-image:latest

# Pull from anywhere
docker pull yourusername/my-image:latest
```

---

## Magento & Docker Operations

### M2 Community Keys

```
f308b8d62f700012680fb3f4992ba400
95e40f57821ece9d6aed943a172246a0
```

### Fix Permissions & Admin User Creation

```bash
# Fix permissions for mydemo2
cd /var/www/html/docker && docker compose exec php-fpm bash -lc "cd /var/www/html/mydemo2 && find var -type d -exec chmod 777 {} \; && find var -type f -exec chmod 666 {} \; && find pub/static -type d -exec chmod 777 {} \; 2>/dev/null || true && find pub/media -type d -exec chmod 777 {} \; 2>/dev/null || true && find app/etc -type f -exec chmod 666 {} \; && echo 'Permissions fixed'"

# Create admin user for mydemo1
docker compose exec php-fpm bash -lc "cd /var/www/html/mydemo1 && bin/magento admin:user:create --admin-user='admin' --admin-password='admin#123' --admin-email='admin@mydemo1.local' --admin-firstname='Admin' --admin-lastname='User'"

# Disable 2FA modules and flush cache for mydemo2
docker compose exec php-fpm bash -lc "cd /var/www/html/mydemo2 && bin/magento module:disable Magento_TwoFactorAuth Magento_AdminAdobeImsTwoFactorAuth && bin/magento cache:flush"
```

---

## Linux Command Reference

### System & User Information

```bash
printenv SHELL      # Print current shell
whoami              # Display current username
id                  # Display user ID and group ID
uname               # Display system information
hostname            # Display system hostname
hostname -i         # Display IP address of hostname
```

### Process & System Monitoring

```bash
top                 # Display real-time system processes
ps                  # Display current processes
df -h ~             # Display disk usage in human-readable format
```

### File Operations

```bash
cp                  # Copy files/directories
mv                  # Move/rename files/directories
rm                  # Remove files/directories
touch               # Create empty file or update timestamp
wc                  # Count lines, words, and characters
```

### Network Commands

```bash
ping                # Test network connectivity
ifconfig            # Display network interface configuration
ip add show eth0    # Display IP address for eth0 interface
```

### Miscellaneous

```bash
date                # Display current date and time
grep                # Search text patterns
man                 # Display manual pages
changemod           # Change file permissions
```

### Directory Listing

```bash
ls dir              # List directory contents
ls -1               # List one file per line
```

---

## Network Configuration

### JIO FIBER LOGIN

**URL:** http://192.168.29.1/platform.cgi?page=disks.html

#### Default Credentials
- **Username:** admin
- **Password:** Jiocentrum

#### Custom Credentials
- **Username:** admin
- **Password:** Mujtaba#1989

---

## Trading & Market Analysis

### Training Steps for Trade Master

1. **Provide market data**: Share current and historical market data, including prices, trading volumes, and sentiment analysis.
2. **Define trading goals**: Specify the type of trading strategy you want me to learn (e.g., day trading, swing trading, long-term investing).
3. **Introduce technical indicators**: Teach me about various technical indicators (e.g., RSI, MACD, Bollinger Bands) and how to apply them.
4. **Simulate trading scenarios**: Engage in mock trading conversations, where you provide market scenarios, and I respond with trading decisions.
5. **Feedback and evaluation**: Correct my responses, providing feedback on my trading decisions, and help me refine my knowledge.
6. **Continuous learning**: Regularly update me with new market information, trends, and analysis to ensure my knowledge stays current.

### Market Example: Solana (SOL)

- **Price:** $83.0200
- **24h Change:** +0.19%
- **Sentiment:** 32.3

> Analysis: The market is relatively stable with minimal price movement.

---

## Credentials

### Admin Credentials

**Email:** admin@myaibuddy.dev  
**Password:** DevPass1234

---
Summary Prompt:
Make sure summurize thie above topic such a way that :
1. It doesnot lose it's main points #### High priority
2. It should be easy to understand and remember, use easy words whereever applicable.
3. It should in such way that you are explaining it to a layman not professional, don't overexplain the things but keep it easy and simple #### Highest priority
4. Provide example if it is appicable example should be easy to understand and explanable why, what and where.
5. Make diagram if applicable.
6. Donot create or bind or attached any urls or links to any description and topics. #### High priority
7. Make it in a way that layman human also can understand add code and other examples. #### High priority
8. Focus on the summary remove difficult words instead use very easy non technical words and sentence.
9. Get information from website also to make it more understandable.
10. Make sure make these notes such a way it should be understandable by layman.
---

You are an expert technical writer and educator. Your task is to produce a developer-focused, structured summary of the module below, making it accessible yet thorough for a technical audience interested in building robust, production-ready Claude-based AI applications.

Read the module content and create a summary that:
- Clearly explains the **purpose** of the module and its main themes.
- Defines and explains all important technical terms (such as prompt, tool, agent, context, and memory) with concise, non-jargony language and concrete examples.
- Highlights the **key problems and challenges** faced when moving from a simple Claude demo to a reliable, scalable, real-world application.  
- Explains, step by step, the core components, techniques, and design patterns taught in the module for safe and robust AI deployment—including prompt design, tool use, response streaming, error recovery, context management, agent workflows, memory, human-in-the-loop review, and bulk processing.
- Provides practical, illustrative examples to demonstrate these concepts in action.
- Clearly states the *main takeaway* and production mindset developers should adopt.
- Presents these elements in an organized, well-formatted structure (using sections, lists, or tables as appropriate).
- Integrates reasoning and explanation *before* final summaries, conclusions, or actionable advice.
- Includes sources where possible if any factual or external claims are made.

# Steps

1. **Purpose and Overview**  
   - Summarize what the module teaches and why it matters for production AI.

2. **Key Terms and Concepts**  
   - Define "prompt," "tool," "agent," "context," and "memory" in clear, non-jargony terms, each with a brief example.
   
3. **Challenges in Productionizing AI**  
   - Analyze and explain the pitfalls of prototype/proof-of-concept Claude apps when deployed in real-world settings.
   
4. **Core Production Techniques**  
   - For each of the following, explain:
       - What it is  
       - Why it matters  
       - How to implement it effectively
       - Common pitfalls to avoid  
     Topics: 
       - Prompt engineering with fixed output formats
       - Extended/chain-of-thought reasoning
       - Controlled and secure tool calls (API/database/etc)
       - Full tool action loops
       - Live/streamed response handling
       - Robust error and interruption handling
       - Context window management and message trimming/summarization
       - Agent architectures for multi-step tasks
       - Memory storage and recall
       - Human-in-the-loop for critical/risky actions
       - Batch/background processing
      
5. **Practical Example**  
   - Provide an outlined scenario (such as e-commerce support), walking through the complete end-to-end workflow, reflecting module best practices.

6. **Main Takeaway and Mindset**
   - Distill the developer-focused lesson and emphasize the importance of thinking beyond proof-of-concept to safe, reliable, scalable deployment.

# Output Format

- Use markdown for headings, bold/italic emphasis, and lists.
- Structure content with clear section headers matching the steps above.
- Use tables or bullet points for side-by-side comparisons, wherever this clarifies complex terms or options.
- Integrate practical examples and scenarios as sub-sections or callout boxes.
- Keep language clear, concise, and technically precise.

# Examples

Example structure and approach:

---
## Purpose and Overview

[Concise summary; e.g., “This module teaches developers how to turn a simple Claude demo into a robust, production-ready AI system by…”]

## Key Terms and Concepts

| Term   | Definition | Example |
|--------|------------|---------|
| Prompt | [Short definition] | [Example of good and bad prompt] |
| ...    | ...        | ...     |

## Main Challenges

- [List and explain problems that arise in production, e.g., long conversations, context window limits, error handling, etc.]

## Core Production Techniques

### Prompt Design
- What: …
- Why: …
- How: …
- Pitfalls: …

[etc. for each topic]

## Practical Example: [Scenario Title]

[Scenario steps, with reasoning before conclusions or summaries. E.g.:]
1. User requests order info…
2. System checks authentication…
3. Backend queries…
4. AI agent summarizes for customer…
5. (Etc.)

## Main Takeaway

[Actionable, developer-focused summary emphasizing robust, safe, cost-effective, scalable Claude system design.]

---

# Notes

- Always provide reasoning and explanation before summarizing lessons or giving best-practice conclusions.  
- Ensure examples illustrate the flow and edge cases.  
- If quoting or paraphrasing specifics, keep them accurate to the source content or mark with brackets/ellipses as placeholders.  
- Do not begin with, or jump to, conclusions or “main points” until stepwise reasoning is complete.

Remember: Your summary should teach developers *how* and *why* to build Claude-powered apps that stay reliable and safe when deployed for real users in production, using practical, organized, and well-explained advice.

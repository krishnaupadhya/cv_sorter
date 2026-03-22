╔═══════════════════════════════════════════════════════════════════════════════╗
║                        CV SORTER — EXECUTION GUIDE                            ║
║                   Production Resume Ranking System v2.0                       ║
╚═══════════════════════════════════════════════════════════════════════════════╝

================================================================================
TABLE OF CONTENTS
================================================================================
1. Prerequisites & Setup
2. Basic Execution Commands
3. Advanced Usage Examples
4. Command Line Arguments Reference
5. Output Structure
6. Troubleshooting Common Issues
7. Performance Tips


================================================================================
1. PREREQUISITES & SETUP
================================================================================

Step 1: Install Python Dependencies
------------------------------------
python3 -m venv venv
# Activate virtual environment (if using one)
source venv/bin/activate  # On macOS/Linux
venv\Scripts\activate     # On Windows

# Install required packages
pip install "groq>=0.9.0"
pip install "sentence-transformers>=2.7.0"
pip install "faiss-cpu>=1.8.0"
pip install "pymupdf>=1.24.0"
pip install "python-docx>=1.1.0"
pip install "pdfplumber>=0.11.0"
pip install "pymupdf4llm>=0.0.17"
pip install "pydantic>=2.0.0"
pip install "rich>=13.0.0"
pip install "tabulate>=0.9.0"
pip install "python-dotenv>=1.0.0"
pip install "langchain>=0.3.0"
pip install "langchain-core>=0.3.0"
pip install "langchain-groq>=0.2.0"
pip install "langchain-ollama>=0.2.0"

Step 2: Get Groq API Key
-------------------------
1. Go to https://console.groq.com
2. Sign up for a free account
3. Navigate to API Keys section
4. Create a new API key (starts with "gsk_...")
5. Copy the key for use in commands below

Step 3: Prepare Your Data
--------------------------
• Create a folder with resume files (PDF or DOCX format)
• Prepare job description file (TXT, PDF, or DOCX format)
• Ensure files are under 2MB and not corrupted/password-protected


================================================================================
2. BASIC EXECUTION COMMANDS
================================================================================

Command Format:
--------------
python main.py --resumes <RESUME_FOLDER> --jd <JOB_DESCRIPTION_FILE> --api-key <YOUR_GROQ_API_KEY>

Example 1: Basic Execution with TXT Job Description
--------------------------------------------------
python main.py \
  --resumes ./RESUME/ \
  --jd job-description.txt \
  --api-key gsk_

Example 2: Using PDF Job Description
------------------------------------
python main.py \
  --resumes ./RESUME/ \
  --jd "Senior Android Developer - Q1 2026.pdf" \
  --api-key gsk_

Example 3: Using DOCX Job Description
-------------------------------------
python main.py \
  --resumes ./RESUME/ \
  --jd job_posting.docx \
  --api-key gsk_


================================================================================
3. ADVANCED USAGE EXAMPLES
================================================================================

Example 4: Generate Reports for Top 5 Candidates Only
-----------------------------------------------------
# Faster execution, focuses on top performers only
python main.py \
  --resumes ./RESUME/ \
  --jd job-description.txt \
  --api-key gsk_YOUR_API_KEY_HERE \
  --top 5

Example 5: Custom Output Directory
----------------------------------
# Save results to a specific folder
python main.py \
  --resumes ./RESUME/ \
  --jd job-description.txt \
  --api-key gsk_YOUR_API_KEY_HERE \
  --output-dir ./custom_results

Example 6: Large Resume Dataset (50+ candidates)
------------------------------------------------
# Increase top-N to get more detailed reports
python main.py \
  --resumes ./RESUME_BATCH_2/ \
  --jd senior-backend-engineer.txt \
  --api-key gsk_YOUR_API_KEY_HERE \
  --top 20

Example 7: Quick Screening Run (Top 3 Only)
-------------------------------------------
# Fastest execution for initial screening
python main.py \
  --resumes ./RESUME/ \
  --jd job-description.txt \
  --api-key gsk_YOUR_API_KEY_HERE \
  --top 3


================================================================================
4. COMMAND LINE ARGUMENTS REFERENCE
================================================================================

Required Arguments:
------------------
--resumes PATH          Path to directory containing resume files (PDF/DOCX)
                       Example: ./RESUME/ or /Users/john/resumes/
                       
--jd PATH              Path to job description file (TXT, PDF, or DOCX)
                       Example: job-description.txt or "Job Posting.pdf"
                       
--api-key KEY          Your Groq API key (starts with "gsk_")
                       Get from: https://console.groq.com

Optional Arguments:
------------------
--provider PROVIDER    LLM provider to use (default: groq)
                       Currently only 'groq' is supported
                       
--top N               Number of top candidates to generate detailed reports for
                       Default: 10
                       Range: 1-100
                       Higher numbers = longer execution time
                       
--output-dir PATH     Directory to save results (default: output/results)
                       Creates timestamped subdirectories automatically


================================================================================
5. OUTPUT STRUCTURE
================================================================================

After execution, results are saved in timestamped folders:

output/results/ranking_YYYYMMDD_HHMMSS/
├── criteria.json              # Extracted job criteria with weights
├── skills.json                # Combined skill keywords (base + job-specific)
├── ranking.json               # Full ranking data (JSON format)
├── ranking.txt                # Human-readable ranking summary
└── cv_sorting_results.txt     # Comprehensive report with skills & gaps

File Descriptions:
-----------------
• criteria.json
  - Job criteria extracted by LLM
  - Each criterion has: name, weight (5-10), explanation
  - Used for algorithmic scoring

• skills.json
  - All skill keywords used for matching
  - Combines base skills + job-specific skills from JD
  - Typically 50-100 keywords

• ranking.json
  - Full ranking data in JSON format
  - Includes: scores, matched/missing criteria, contact info
  - Suitable for integration with other systems

• ranking.txt
  - Human-readable ranking summary
  - Lists all candidates with scores and brief assessments
  - Quick overview format

• cv_sorting_results.txt
  - Most comprehensive report
  - Section 1: Ranking table
  - Section 2: Detailed candidate profiles with skills and gaps
  - Best for detailed analysis and hiring decisions


================================================================================
6. TROUBLESHOOTING COMMON ISSUES
================================================================================

Issue 1: "API key is required for Groq provider"
-----------------------------------------------
Solution: Ensure you provide --api-key argument with valid Groq API key
Command: python main.py --resumes ./RESUME/ --jd job-description.txt --api-key gsk_...

Issue 2: "No valid resumes found"
---------------------------------
Possible causes:
  • Files are larger than 2MB (compress or split them)
  • Files are password-protected (remove protection)
  • Files are corrupted (try opening them manually)
  • Wrong file format (only PDF and DOCX supported)
Solution: Verify resume files are valid PDF/DOCX under 2MB

Issue 3: "Job description file not found"
-----------------------------------------
Solution: Check file path and name (use quotes for paths with spaces)
Command: python main.py --resumes ./RESUME/ --jd "Job Posting Q1 2026.pdf" --api-key gsk_...

Issue 4: "Failed to parse LLM response as JSON"
----------------------------------------------
Possible causes:
  • API rate limit exceeded (wait and retry)
  • Network connectivity issues
  • Groq API service temporarily unavailable
Solution: Wait 30 seconds and retry. If persists, check Groq status page

Issue 5: ImportError for langchain-groq
---------------------------------------
Solution: Install missing dependency
Command: pip install langchain-groq

Issue 6: Slow Execution Time
----------------------------
Solutions:
  • Reduce --top value (e.g., --top 5 instead of --top 20)
  • Ensure stable internet connection for Groq API
  • Close other applications to free up system resources


================================================================================
7. PERFORMANCE TIPS
================================================================================

Speed Optimization:
------------------
1. Use --top 5 or --top 3 for quick screening runs
2. Process resumes in smaller batches (10-20 at a time)
3. Keep job descriptions concise (under 1000 words)
4. Ensure fast internet connection (Groq API is cloud-based)

Quality Optimization:
--------------------
1. Provide detailed job descriptions (500-1000 words ideal)
2. Include specific technical skills in JD
3. Mention experience requirements explicitly
4. Use --top 10 or higher for comprehensive analysis

Cost Optimization (Groq Free Tier):
-----------------------------------
• Groq free tier offers generous limits
• 2 LLM calls per execution (job analysis + top-N reports)
• Example: 50 resumes with --top 10 = 11 LLM calls total
• Monitor usage at https://console.groq.com


================================================================================
EXAMPLE OUTPUT INTERPRETATION
================================================================================

Understanding Scores:
--------------------
• Combined Score (0-100): Blended algorithmic + semantic similarity
  - 75-100: Strong Fit (highly recommend for interview)
  - 55-74:  Moderate Fit (consider for interview)
  - 0-54:   Weak Fit (likely not suitable)

• Algorithm Score (0-100): Keyword matching against criteria
  - Measures explicit skill mentions
  - Higher = more matching keywords

• Semantic Score (0-100): Embedding similarity
  - Measures contextual relevance
  - Captures related experience and domain knowledge

Recommendation:
--------------
Start with Strong Fit candidates (75+) for interviews, then move to 
Moderate Fit (55-74) if you need more options. Weak Fit (<55) candidates
typically have significant skill gaps.


================================================================================
ADDITIONAL RESOURCES
================================================================================

• Project README: README.md
• Setup Guide: SETUP.md
• Project Report: PROJECT_REPORT.md
• Requirements: requirements.txt
• Configuration: config.py

For questions or issues:
• Check error messages carefully (they include helpful hints)
• Review this execution guide
• Verify prerequisites and setup steps

Last Updated: March 22, 2026
Version: 2.0 (Groq-only edition)

(linkcheck-optimization)=
# Linkcheck optimization


This prompt should allow you to replicate the documentation linkcheck optimization test.

Created with:
: LLM: Claude Sonnet 4.5
: Agent: GitHub Copilot

-----

Prompt: Benchmark and Optimize Sphinx Linkcheck Performance

I need to benchmark and optimize the linkcheck speed in my Sphinx documentation project. Please follow this systematic approach:

Initial Benchmark: Run make linkcheck > linkcheck.txt with time measurement to establish the baseline performance.

Analysis: Analyze the linkcheck output to identify:

Rate-limited domains causing delays
Timeout issues
Current configuration settings (timeout, retries, workers)
Broken links, redirects, and successful checks
Any patterns in slow responses
Implement Optimizations (in this order):

Add rate-limited domains to linkcheck_ignore in conf.py (use regex patterns)
Reduce linkcheck_timeout to 15 seconds
Reduce linkcheck_retries to 2
Benchmark after these changes
Parallel Workers Optimization:

Add linkcheck_workers configuration (suggest starting at 20 for network I/O-bound tasks)
Benchmark again with workers enabled
Verify no new rate-limiting issues
Report Results: Provide a comparison table showing:

Baseline time
Time after config optimization
Time after worker optimization
Percentage improvements for each step
Total time saved
Important: Do NOT add genuinely broken links to the ignore list - only exclude rate-limited or slow domains. Make all changes to conf.py, then benchmark after each major change to measure incremental improvements.

This prompt captures the step-by-step methodology we used while giving you the flexibility to adapt to different results.

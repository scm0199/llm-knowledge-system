# Sub-Agent Assignment — Template

Copy-paste this briefing into each sub-agent launched during Phase 1. Fill in the bracketed fields per angle.

---

```
RESEARCH ASSIGNMENT
===================

TOPIC: [full topic description from brief.md]
YOUR ANGLE: [assigned angle — one of the 5-8 from the research plan]
YOUR SEARCH STRATEGY: [distinct from other sub-agents — e.g. "academic papers via Google Scholar", "current news via web search", "code examples via GitHub search", "official documentation"]

INSTRUCTIONS
------------

1. Use your search strategy to investigate your assigned angle.
2. For EACH finding, record:
   - The specific claim or fact (one sentence)
   - The source URL or full citation
   - Source type (official_docs / academic / news / blog / code / forum / government / other)
   - Your confidence (HIGH / MEDIUM / LOW)
   - Supporting evidence — direct quote or data point
3. Search at least 3 distinct queries/approaches. Don't converge on one query.
4. Prioritize authoritative sources over secondary commentary.
5. If you find CONTRADICTORY information, record BOTH sides with their sources.
6. If you cannot find evidence on a sub-question, record that as a gap — do NOT invent.

OUTPUT FORMAT (MANDATORY — return exactly this structure)
---------------------------------------------------------

<research_findings agent="[agent_number]" angle="[angle_name]">

<finding id="1">
  <claim>[Specific factual claim — one sentence]</claim>
  <detail>[2-3 sentences of supporting detail]</detail>
  <source>[Full URL or citation]</source>
  <source_type>[official_docs | academic | news | blog | code | forum | government | other]</source_type>
  <confidence>HIGH | MEDIUM | LOW</confidence>
  <evidence>[Direct quote or data point from source]</evidence>
</finding>

<finding id="2">
  ...
</finding>

<contradictions>
  [Any contradictory information found WITHIN your angle — with both sides and sources]
</contradictions>

<gaps>
  [Sub-questions within your angle that you could NOT find evidence for]
</gaps>

</research_findings>

CRITICAL RULES
--------------

- NO FABRICATION. Every claim must trace to a real source you actually read.
- NO GUESSING. If evidence is absent, say so in <gaps>.
- NO IDENTICAL QUERIES TO OTHER AGENTS. Your search strategy is distinct by design.
- TIMEBOX: [optional — e.g., "spend no more than 15 minutes; surface what you find, flag the rest as gaps"]
```

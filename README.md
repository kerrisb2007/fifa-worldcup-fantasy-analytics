FIFA World Cup Fantasy Analytics & Pacing Matrix
An interactive Tableau dashboard that bridges traditional soccer analytics with data models optimized for fantasy sports platforms. By mapping historical team consistency against match volatility, this project isolates the high-engagement, "high-variance" game environments that drive season-long fantasy lineups, user interaction, and prop-wagering behavior.
Live Dashboard Link: https://public.tableau.com/views/FIFAWorldCupFantasyAnalyticsPacingMatrix/FIFAWorldCupFantasyAnalyticsMatrix?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link

Dataset Profile
Source: Historical FIFA World Cup Match Results (1930 – Present)
Original Grain: One row per individual match fixture, detailing match metrics such as Year, Round, Home Team, Away Team, Home Team Goals, and Away Team Goals.
Target Grain: Restructured into an analytical data model mapping cross-era performance aggregates per individual country.

Data Cleansing & Architectural Pipeline
Raw sports tournament data inherently contains structural fragments due to geopolitical changes, formatting variations, and historical shifts. The following steps were implemented entirely within Tableau to standardize the dataset without destructive alterations to the source schema:
1. Geopolitical Lineage Consolidation (Grouping)
To preserve historical continuity and prevent mathematical fragmentation in long-term trend lines, defunct or changing national entities were mapped to their FIFA-recognized modern institutional successors:
Germany FR → Consolidated into Germany
Serbia & Montenegro / Yugoslavia → Consolidated into Serbia
Soviet Union / USSR → Consolidated into Russia
Note: Republic of Ireland and Northern Ireland were explicitly kept separate to preserve distinct international footballing bodies.
2. Character Encoding Scrubber
Special character translation errors (e.g., character display limits on accent marks like CÃ´te d'Ivoire) were systematically intercepted using layout group mapping to output clean, consumer-facing string values (Ivory Coast).
3. Primary Key Generation (Match ID)
Because historic rematches can occur in different stages of the exact same tournament year (e.g., meeting in the group stage and later in the knockout rounds), a bulletproof, unique primary key was engineered to prevent aggregate data compression:
// Formula for Match ID
STR([Year]) + "_" + [Round] + "_" + [Unified Team Field]

Core Engineering & Calculation Architecture
To pass rigorous Salesforce Data Analyst criteria and address the business logic used by sports gaming platforms like Sleeper, several complex calculations were designed:
1. Matchup Normalization (Unified Team Field)
Because a country could appear in either the Home Team or Away Team column depending on the fixture draw, alphabetical string sorting was utilized to force matching team matchups into a single, unified text bucket:
IF [Home Team Cleaned] < [Away Team Cleaned] THEN [Home Team Cleaned] + " vs " + [Away Team Cleaned]
ELSE [Away Team Cleaned] + " vs " + [Home Team Cleaned]
END
2. Multi-Vector Aggregation (Total Matches Played)
To count every game a team played across history regardless of home/away classification, a non-null distinct validation check was implemented with a safe-fail zero fallback (ZN):
ZN(COUNTD(IF NOT ISNULL([Home Team Cleaned]) THEN [Match ID] END)) + 
ZN(COUNTD(IF NOT ISNULL([Away Team Cleaned]) THEN [Match ID] END))
3. Level of Detail Mastery (Team Win Rate (LOD))
A nested FIXED LOD Expression calculates a team's entire historical win percentage across both columns independent of the visual shelf context or dimensions dropped onto the Marks card:
{ FIXED [Home Team Cleaned] : SUM(IF [Home Team Goals] > [Away Team Goals] THEN 1 ELSE 0 END) } / 
{ FIXED [Home Team Cleaned] : COUNT([Match ID]) }
4. Proprietary Product Index (Fantasy Excitement Index)
A custom, weighted calculation merging central tendency (Average) with statistical dispersion (Standard Deviation). This score measures the overall volume and unpredictability of scoring environments:
(AVG([Home Team Goals] + [Away Team Goals]) * 0.6) + (STDEV([Home Team Goals] + [Away Team Goals]) * 0.4)
60% Weighting (AVG): Establishes the scoring baseline required to generate steady, active fantasy points.
40% Weighting (STDEV): Captures high-volatility "boom-or-bust" game loops that generate user app engagement, wagering drama, and critical lineup decisions.

Interactive Features & User Control
Dynamic Reference Quadrants: Replaced static analytical averages with interactive float sliders (Win Rate Threshold Slider and Excitement Target Level), giving the final user the power to adjust benchmarks and dynamically group teams into behavioral quadrants.
Visual Level of Detail Split: The integration of the binary Is High Scoring Match dimensions allows users to instantly view "production splits," showcasing exactly how a team's fantasy matrix accelerates during high-scoring environments.





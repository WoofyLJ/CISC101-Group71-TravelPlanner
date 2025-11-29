### **Module 2 — Plan Builder (Options → Days)**

Create a short list of candidate activities (e.g., attractions, restaurants, parks).  
Each activity includes type, estimated duration, cost range, and distance.
Module 2 — Plan Builder (Options → Days)
Inputs Required
Lodging location (coordinates or address)

Use a simple loop to build days:
Travel dates

for each day:  
    pick Morning activity (near lodging)  
    pick Midday activity (close by)  
    pick Afternoon activity (different theme)  
    pick Evening restaurant or optional event
Daily budget range

User preferences (themes, cuisine, must-see items)

Mobility constraints (walking distance, transit limits, accessibility needs)

Weather conditions or forecast

Activity Format (consistent schema)
Each candidate activity includes:

Name

Type (attraction, restaurant, park, event)

Theme tags (e.g., cultural, outdoor, food)

Location/coordinates

Estimated duration

Cost range (per person)

Distance/travel time from lodging or previous activity

Opening hours

Accessibility/booking notes

Weather dependency (indoor/outdoor)

Day-Building Loop
For each day:

Morning activity

Select within proximity threshold of lodging

Must align with opening hours

Prefer indoor or weather-robust option

Midday activity

Choose close to morning activity (routing time ≤ threshold)

Include lunch slot if applicable

Check cumulative cost and time fit

Afternoon activity

Enforce different primary theme from earlier activities

Validate open hours and add rest buffer if needed

Evening restaurant or optional event

Near afternoon activity

Respect cuisine preferences and budget

Provide fallback option if booking/weather issues arise

Validation & Feasibility Checks
Opening hours overlap with planned time blocks

Travel time between activities within thresholds

Total day length ≤ 8–10 hours including transit

Daily cost range fits user budget

Backtrack and reseat choices if plan invalid

Robustness Rules
Bad weather: Swap outdoor activities for indoor alternatives while maintaining theme diversity

Tight budgets: Prioritize free/low-cost options (parks, walks, casual dining)

Few options available: Relax non-critical constraints (distance, duration) one at a time, but never violate hard constraints (budget, open hours)

Mobility constraints: Adjust proximity thresholds and avoid inaccessible routes

Overbooking prevention: Insert downtime slots if cumulative duration exceeds comfort threshold

Output Format
Each day is presented as:

Morning / Midday / Afternoon / Evening blocks

For each activity: name, type, theme tags, duration, open hours, cost range, travel time, and one-line note (e.g., booking required, weather contingency)

Daily summary: total estimated cost range, total travel time, and contingency notes

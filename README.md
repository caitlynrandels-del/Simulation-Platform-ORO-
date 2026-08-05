OROBORO CLINICAL SIMULATION PLATFORM
=====================================

An agent-based infectious disease simulation with a live 3D visualization
and researcher-facing dashboard.


WHAT'S INCLUDED
---------------
oroboro_clinical_platform.py   - Backend simulation engine + API (FastAPI)
oroboro_clinical_interface.html - Frontend dashboard (3D view, controls, readout)


REQUIREMENTS
------------
- Python 3.10+ (tested on 3.10)
- pip packages: fastapi, uvicorn, python-multipart

Install with:

    pip install fastapi uvicorn python-multipart


HOW TO RUN
----------
You need TWO things running at once: the backend API, and a simple file
server for the HTML dashboard. They must be in separate terminal windows
since both stay running continuously.

1. Start the backend (serves the simulation on port 8000):

       cd path\to\this\folder
       python .\oroboro_clinical_platform.py

   You should see a line like:
       Uvicorn running on http://127.0.0.1:8000

2. In a second terminal, start a static file server for the dashboard
   (serves the HTML on port 8080):

       cd path\to\this\folder
       python -m http.server 8080

3. Open the dashboard in your browser:

       http://localhost:8080/oroboro_clinical_interface.html

   IMPORTANT: Open it via http://localhost:8080/..., NOT by double-clicking
   the HTML file directly. Opening it as a file:// path will break the
   connection to the backend in most browsers.

If you see "BACKEND OFFLINE" in the dashboard, check that step 1's
terminal window is still running and didn't crash — any Python error
will print there.


ONE-SHOT LAUNCH (PowerShell)
-----------------------------
To start everything at once from a single PowerShell window:

    cd path\to\this\folder
    Start-Process powershell -ArgumentList "-NoExit","-Command","cd '$pwd'; python .\oroboro_clinical_platform.py"
    Start-Process powershell -ArgumentList "-NoExit","-Command","cd '$pwd'; python -m http.server 8080"
    Start-Sleep -Seconds 3
    start http://localhost:8080/oroboro_clinical_interface.html

This opens two new PowerShell windows (one per server) and then opens the
dashboard in your default browser.

To stop everything, close both server windows, or run:

    Get-Process python | Stop-Process -Force


DASHBOARD FEATURES
-------------------
Left panel:
  - Connection status (online/offline)
  - Live population telemetry (raw JSON: state counts, bed capacity)
  - Intervention Strength slider + Save/Load Scenario buttons
    (scenario is kept in memory only — it resets if you reload the page)
  - Synthetic Data Generator — regenerates the population at a chosen size

Right panel:
  - Researcher Readout — a live, timestamped event log showing exposures,
    state transitions (infectious / hospitalized / recovered), and
    population resets as they happen

3D view (center):
  - Each sphere is one simulated patient, colored by current state:
      Blue   = Susceptible
      Yellow = Exposed
      Red    = Infectious
      Green  = Recovered
      White  = Hospitalized
  - Scroll the mouse wheel over the view to zoom in/out (clamped range)
  - The scene auto-rotates slowly


API ENDPOINTS (backend, port 8000)
------------------------------------
GET /telemetry
    Advances the simulation one step and returns current population
    counts by state, plus hospital bed usage.

GET /visual_agents
    Returns position + color for every patient, used to render the
    3D scene.

GET /events?limit=40
    Returns the most recent simulation events (exposures, state
    changes, resets) for the researcher readout panel.

GET /generate_test_population?size=N
    Rebuilds the population with N agents (10-5000) and reseeds a
    small number as infectious. Also clears and restarts the event log.

GET /patient/{id}
    Returns detailed info + calculated risk score for a single patient.

GET /
    Serves a minimal built-in telemetry-only page (not the main
    dashboard — use oroboro_clinical_interface.html for the full UI).


TROUBLESHOOTING
----------------
"BACKEND OFFLINE" in the dashboard:
  - Confirm the backend terminal window is still open and running
  - Run `netstat -ano | findstr :8000` — if nothing appears, the
    backend isn't running; restart it

"Generator failed" / NetworkError in browser console:
  - Same as above — usually means the backend isn't running or was
    just restarted and needs a moment

CORS errors in browser console:
  - The backend already has CORS enabled for all origins, so this
    usually means the request never reached the server at all
    (backend not running, wrong port, or it crashed)

No spheres visible in the 3D view:
  - Make sure you're viewing via http://localhost:8080/..., not a
    file:// path
  - Try scrolling to zoom in — spheres may be too small/far to see
    at the default zoom level depending on population size# Simulation-Platform-ORO-
Agent-based epidemiological modeling engine with stateful patient agents, transmission dynamics, risk scoring, hospital resource forecasting, synthetic population generation, and live telemetry APIs powering a Three.js clinical visualization dashboard

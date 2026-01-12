# Remote Data Caching Engine (WordPress)

 Technical Purpose
This plugin is a technical demonstration of Performance Optimization and API Resilience in a WordPress environment. It solves the critical problem of external API bottlenecks in high-traffic enterprise sites.

 Key Engineering Competencies
* Transients API Implementation:** Implements `get_transient` and `set_transient` to ensure the server makes only 1 external request per hour, regardless of page views.
* Resilient API Handling: Uses the `wp_remote_get()` wrapper with `is_wp_error()` checks to prevent site crashes during API downtime.
* Data Integrity: Strict sanitization using `floatval()` and `esc_html()` before rendering data to the frontend.
* Object-Oriented Design: Encapsulates logic within a singleton-style PHP Class for clean, maintainable code.

 Tech Stack
* PHP: OOP Architecture.
* WordPress API: Transients API, HTTP API (Remote Get).
* JSON: Parsing and validation of external data structures.

 🚀 Impact
By implementing this caching strategy, site administrators can avoid API rate-limiting issues and reduce page load times by eliminating synchronous external HTTP calls on every request.

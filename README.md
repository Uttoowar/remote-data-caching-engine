<?php
/**
 * Plugin Name: Remote Data Caching Engine
 * Description: Demonstrates REST API integration with Transients API caching for enterprise scalability.
 * Version: 1.0
 * Author: Uttoorwar Meher Mani
 */

if ( ! defined( 'ABSPATH' ) ) exit; // Security: Prevent direct access

class RemoteDataEngine {

    // Unique key for the cache to avoid collisions
    private $cache_key = 'rt_enterprise_api_cache';
    
    // Cache duration: 1 hour (3600 seconds)
    private $cache_expiry = 3600;

    public function __construct() {
        // Register shortcode: [api_data_ticker]
        add_shortcode( 'api_data_ticker', array( $this, 'render_data_view' ) );
    }

    /**
     * Logic: Fetches data from Remote API or returns cached version
     */
    private function get_validated_data() {
        // 1. Check for cached version first (Transients API)
        $cached_response = get_transient( $this->cache_key );

        if ( false !== $cached_response ) {
            return $cached_response;
        }

        // 2. Fetch from External API (Example: Public Crypto Price API)
        $api_url  = 'https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd';
        $response = wp_remote_get( $api_url );

        // 3. Error Handling: Check for API failures
        if ( is_wp_error( $response ) ) {
            return false; 
        }

        $body = json_decode( wp_remote_retrieve_body( $response ), true );

        // 4. Save to Cache for 1 hour to prevent redundant server calls
        set_transient( $this->cache_key, $body, $this->cache_expiry );

        return $body;
    }

    /**
     * View: Renders the data securely in the frontend
     */
    public function render_data_view() {
        $data = $this->get_validated_data();

        if ( ! $data ) {
            return '<p>Service temporarily unavailable. Please try again later.</p>';
        }

        $price = floatval( $data['bitcoin']['usd'] );

        ob_start(); // Output buffering for clean shortcode rendering
        ?>
        <div class="api-ticker-card" style="border-left: 5px solid #2271b1; background: #f6f7f7; padding: 15px; margin: 10px 0;">
            <h4 style="margin-top:0;">📊 Real-time Data Feed</h4>
            <p>Bitcoin (BTC/USD): <strong>$<?php echo esc_html( number_format( $price ) ); ?></strong></p>
            <small style="color: #646970;">Performance: This data is server-side cached for 60 minutes.</small>
        </div>
        <?php
        return ob_get_clean();
    }
}

new RemoteDataEngine();

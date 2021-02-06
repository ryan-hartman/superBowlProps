<script>
	import { Router } from '@roxi/routify'
    import { routes } from '../.routify/routes'	
</script>

<style global>
	@import "../static/chota.min.css";
	@import "../static/app.css";
	/* @import "../static/global.css"; */

    :global(:root) {
        --color-primary: #14854F;
        --color-lightGrey: #d2d6dd;
        --color-grey: #747681;
        --color-darkGrey: #3f4144;
        --color-error: #d43939;
        --color-success: #28bd14;
        --grid-maxWidth: 120rem;
        --grid-gutter: 2rem;
        --font-size: 1.8rem;
        --font-family: "Helvetica Neue", sans-serif;
		--bg-color: rgb(20,29,38);
		--font-color:rgb(255,255,255);
	}	

</style>

<Router {routes} />

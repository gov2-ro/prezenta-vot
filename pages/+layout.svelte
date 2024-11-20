<script>
import "@evidence-dev/tailwind/fonts.css";
import "../app.css";
import { EvidenceDefaultLayout } from "@evidence-dev/core-components";
export let data;
</script>

<svelte:head>
 	
</svelte:head>

<EvidenceDefaultLayout 
	{data} title="Prezență alegeri"
	-logo="/assets/votcorect-alb.svg"
	maxWidth=1800
	fullWidth=true
	githubRepo="https://github.com/gov2-ro/prezenta.roaep.ro"
>
	<slot slot="content" />
</EvidenceDefaultLayout>

 
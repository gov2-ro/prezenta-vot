<script>
import "@evidence-dev/tailwind/fonts.css";
import "../app.css";
import { EvidenceDefaultLayout } from "@evidence-dev/core-components";
export let data;
</script>

<svelte:head>
 	
</svelte:head>

<EvidenceDefaultLayout 
	{data} title="Prezență alegeri - Vot Corect" 	 
	maxWidth=1800
	fullWidth=true
	
>
	<slot slot="content" />
</EvidenceDefaultLayout>

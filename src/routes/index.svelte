<script context="module">
  import Head from '$components/head.svelte'
  import ProjectCard from '$components/project-card.svelte'
  import { client } from '$lib/graphql-client'
  import { authorsQuery, projectsQuery } from '$lib/graphql-queries'
  import {
    authorsStore,
    siteMetadataStore,
  } from '$stores/site-metadata'

  export const load = async () => {
    const [authorRes, projectsRes] = await Promise.all([
      client.request(authorsQuery),
      client.request(projectsQuery),
    ])
    const { authors } = authorRes
    const { projects } = projectsRes

    return {
      props: {
        projects,
        authors,
      },
    }
  }
</script>

<script>
  export let projects
  export let authors

  const {
    siteUrl,
    name: siteName,
    description,
    openGraphDefaultImage,
  } = $siteMetadataStore
  const { name: authorName } = $authorsStore
</script>

<Head
  title={`${siteName}`}
  {description}
  image={openGraphDefaultImage.url}
  url={`${siteUrl}`}
/>

{#each authors as { name, intro, picture: { url } }}
<h1 class="font-bold text-center mb-20 text-5xl">
  {name}
</h1>
  <div class="flex mb-40 items-end">
    <div class="mr-6">
      <p class="text-xl mb-4">{intro}</p>
    </div>

    <img class="mask mask-squircle h-48" src={url} alt={name} />
  </div>
{/each}

<div
  class="grid gap-10 md:grid-cols-4 md:px-10 lg:grid-cols-6 lg:-mx-52"
>
  {#each projects as { name, slug, description, image }}
    <ProjectCard {name} {description} url={image[0].url} {slug} />
  {/each}
</div>

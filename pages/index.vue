<template>
  <div class="wrapper-small md:px-10">
    <div class="h-96 mt-5">
      <Hero />
    </div>

    <Blogs :posts="posts" title="Featured blogs" />
    <Projects :projects="projects" :github-link="githubLink" />
  </div>
</template>

<script>
export default {
  async asyncData({
    $axios,
    $notion,
    $config: { githubLink, githubUsername, notionTableId },
  }) {
    const projects = await $axios
      .get(
        `https://api.github.com/search/repositories?q=user:${githubUsername}&sort=stars&per_page=3`
      )
      .catch((errors) => {
        // console.log(errors)
      })
    const pageTable = await $notion.getPageTable(notionTableId)
    const posts = pageTable
      .filter((page) => page.public)
      .sort((a, b) => new Date(b.created_at) - new Date(a.created_at))
    return { posts, projects: projects.data.items, githubLink }
  },
}
</script>

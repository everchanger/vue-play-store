<template>
  <div class="container main-container">
      <div class="col col-12">
        <!-- Content will be placed here -->
        <slot></slot>
      </div>
  </div>
</template>

<style>

	.main-container {
		background-color: #b4b4b4;
		color: #3a3a3a;
		height: 100%;
	}
</style>

<script>
  /* ============
   * Minimal Layout
   * ============
   *
   * Used for the register and login pages.
   *
   * Layouts are used to store a lot of shared code.
   * This way the app stays clean.
   */

  export default {
    /**
     * The name of the layout.
     */
    name: 'minimal-layout',
  };
</script>

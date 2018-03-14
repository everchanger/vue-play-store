<template>
  <div class="container-fluid main-container">
      <div class="col col-12">
        <!-- Content will be placed here -->
        <slot></slot>
      </div>
  </div>
</template>

<style>
	body {
		background-color: #429fd4;
	}

	.main-container {
		background-color: #52bfff;
		color: #3a3a3a;
		padding-bottom: 80px;
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

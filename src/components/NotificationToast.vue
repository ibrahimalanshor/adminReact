<template>
  <CToast :autohide="false">
    <CToastHeader closeButton>
      <svg
        class="rounded me-2"
        width="20"
        height="20"
        xmlns="http://www.w3.org/2000/svg"
        preserveAspectRatio="xMidYMid slice"
        focusable="false"
        role="img"
      >
        <rect width="100%" height="100%" fill="#007aff"></rect>
      </svg>
      <span class="me-auto fw-bold">CoreUI for Vue.js</span>
      <small>7 min ago</small>
    </CToastHeader>
    <CToastBody>Hello, world! This is a toast message.</CToastBody>
  </CToast>
</template>

<script>
export default {
  name: 'NotificationToast',
}
</script>

<style></style>

<template>
  <a-scene
    xrweb
    tap-place
    xrextras-almost-there
    xrextras-loading
    xrextras-runtime-error
  >
    <!-- We can define assets here to be loaded when A-Frame initializes -->
    <a-assets>
      <!-- Credit to Poly by Google for the model: https://poly.google.com/view/6pwiq7hSrHr -->
      <a-asset-item id="treeModel" src="tree.glb" />
    </a-assets>

    <!-- The raycaster will emit mouse events on scene objects specified with the cantap class -->
    <a-camera
      position="0 8 0"
      raycaster="objects: .cantap"
      cursor=" fuse: false; rayOrigin: mouse;"
    />

    <a-entity light="type: directional; intensity: 0.8;" position="1 4.3 2.5" />

    <a-light type="ambient" intensity="1" />

    <!-- Adding the cantap class allows the ground to be clicked -->
    <a-entity
      id="ground"
      class="cantap"
      geometry="primitive: box"
      material="color: #ffffff; transparent: true; opacity: 0.0"
      scale="1000 2 1000"
      position="0 -1 0"
    />
  </a-scene>
</template>

<script>
export default {
  mounted() {
    AFRAME.registerComponent("tap-place", {
      init: function() {
        const ground = document.getElementById("ground");
        ground.addEventListener("click", event => {
          // Create new entity for the new object
          const newElement = document.createElement("a-entity");

          // The raycaster gives a location of the touch in the scene
          const touchPoint = event.detail.intersection.point;
          newElement.setAttribute("position", touchPoint);

          const randomYRotation = Math.random() * 360;
          newElement.setAttribute("rotation", "0 " + randomYRotation + " 0");

          newElement.setAttribute("visible", "false");
          newElement.setAttribute("scale", "0.0001 0.0001 0.0001");

          newElement.setAttribute("gltf-model", "#treeModel");
          this.el.sceneEl.appendChild(newElement);

          newElement.addEventListener("model-loaded", () => {
            // Once the model is loaded, we are ready to show it popping in using an animation
            newElement.setAttribute("visible", "true");
            newElement.setAttribute("animation", {
              property: "scale",
              to: "0.01 0.01 0.01",
              easing: "easeOutElastic",
              dur: 800
            });
          });
        });
      }
    });
  }
};
</script>

<style>
#__nuxt,
#__layout {
  width: 100vw;
  height: 100vh;
}
</style>

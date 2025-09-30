<script>
import { T } from '@threlte/core';

  import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
  import { useLoader } from '@threlte/core';

  import duckgltf from '$lib/assets/yellow_rubber_duck/Rubbish_Duck.gltf';

  const gltf = useLoader(GLTFLoader).load(duckgltf);
</script>
<T.Mesh position.y={1}>
<T.BoxGeometry args={[1, 2, 1]} />
<T.MeshBasicMaterial color="hotpink" />
</T.Mesh>
{#if $gltf}
  <T is={$gltf.scene} />
{/if}
<template>
    <Lunchbox
        :cameraPosition="[0, 4, 10]"
        :cameraLook="[0, 0, 0]"
        background="antiquewhite"
    >
    </Lunchbox>
</template>

<script setup lang="ts">
// Uses Daniel Esteban's three-raymarching:
// https://twitter.com/DaniGatunes/status/1491481140133347329
import * as THREE from 'three'
import { onBeforeRender, useCamera, useScene } from 'lunchboxjs'
import Raymarcher from 'three-raymarcher'

const count = 10

const raymarcher = new Raymarcher({
    entities: new Array(count).fill(undefined).map((v, i) => {
        return {
            color: new THREE.Color(`hsl(0, 100%, ${(i / count) * 100}%)`),
            position: new THREE.Vector3(i, 0, 0),
            rotation: new THREE.Quaternion(0, 0, 0, 1),
            scale: new THREE.Vector3(1, 1, 1),
            shape: Raymarcher.shapes.sphere,
        }
    }),
    resolution: 0.5,
})

useScene((scene) => {
    scene.add(raymarcher)
})

let camera: THREE.PerspectiveCamera

useCamera((cam) => {
    camera = cam
    // cam.position.y = 3
})
const up = new THREE.Vector3(0, 1, 0)
const transform = new THREE.Matrix4()

onBeforeRender(() => {
    transform.makeRotationAxis(up, 1)
    console.log(raymarcher)

    raymarcher.userData?.entities?.forEach((e: any, i: number) => {
        e.position.x =
            Math.sin(Date.now() * 0.001 + i * 5) *
            Math.sin(Date.now() * 0.0002) *
            5
        e.position.y =
            Math.cos(Date.now() * 0.001 + i * 10) *
            Math.sin(Date.now() * 0.00025) *
            3
        e.position.z =
            Math.sin(Date.now() * 0.001 + i * 2) *
            Math.sin(Date.now() * 0.0003) *
            4
        e.position.applyMatrix4(transform)
    })
})
</script>
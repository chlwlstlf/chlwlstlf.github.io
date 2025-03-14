---
layout: single
title: "[React] R3F를 이용한 3D 웹 개발"
categories: react
toc: true
toc_sticky: true
---

# React Three Fiber

[R3F 강의 유튜브 링크](https://www.youtube.com/watch?v=Sg6OcVxe64k&list=PLe6NQuuFBu7HUeJkowKRkLWwkdOlhwrje&index=1){: .btn .btn--primary}

## <mark class="pink">📌설치</mark>

vite로 프로젝트 시작

```bash
npm create vite@latest
```

three.js 설치

```bash
npm install three @react-three/fiber @react-three/drei
```

@react-three/fiber는 React 18을 요구하기 때문에, React 19과 호환되는지 확인해 보고, 만약 필요하면 React 18로 버전을 낮춰야 한다.

```bash
npm install react@18 react-dom@18
```

<br>
<br>

## <mark class="pink">📌R3F의 Canvas</mark>

**Scene**  
3D 장면: 3D 모델, 조명, 카메라

**Renderer**  
GPU를 이용해서 빠르게 3D 그래픽을 렌더링하는 것

**Camera**  
동일한 Scene에서도 카메라로 보는 방향에 따라 다르게 연출할 수 있다.

<br>

**코드**

App.jsx에서 MyElement3D를 `<Canvas>`로 감싼 후 아래와 같이 코드를 작성해주면,  
계속 y축으로 돌아가는 정육면체를 만들 수 있다.

`useFrame`을 사용하여 계속 회전시킬 수 있다.

```jsx
import { useFrame } from "@react-three/fiber";
import { useRef } from "react";

const MyElement3D = () => {
  const refMesh = useRef();
  useFrame((state, delta) => {
    refMesh.current.rotation.y += delta;
  });

  return (
    <>
      <directionalLight position={[1, 1, 1]} />

      <mesh ref={refMesh} rotation-y={(45 * Math.PI) / 180}>
        <boxGeometry />
        <meshStandardMaterial color="#e67e22" />
      </mesh>
    </>
  );
};

export default MyElement3D;
```

<br>
<br>

## <mark class="pink">📌변환</mark>

**axesHelper**  
축을 나타내어 방향을 알기 쉽게 도와준다.  
x: 빨강, y: 연두, z: 파랑

**OrbitControls**  
마우스 움직임으로 화면을 볼 수 있고, 확대나 축소가 가능하게 해준다.

**boxGeometry**  
정육면체 도형을 생성한다.

**sphereGeometry**  
구 도형을 생성한다.

**meshStandardMaterial**  
도형의 색상, 재질, 투명도 등을 조정할 수 있다.

```jsx
import { OrbitControls } from "@react-three/drei";
import { useFrame } from "@react-three/fiber";
import { useRef } from "react";
import * as THREE from "three";

const MyElement3D = () => {
  const refMesh = useRef();

  useFrame((state, delta) => {
    refMesh.current.rotation.z += delta;
  });

  return (
    <>
      <directionalLight position={[1, 1, 1]} />

      <axesHelper scale={10} />
      <OrbitControls />

      <mesh
        ref={refMesh}
        position={[0, 2, 0]}
        rotation-z={THREE.MathUtils.degToRad(45)}
        scale={[2, 1, 1]}
      >
        <boxGeometry />
        <meshStandardMaterial
          color="#e67e22"
          opacity={0.5}
          transparent={true}
        />
        <axesHelper />

        <mesh scale={[0.1, 0.1, 0.1]} position-y={2}>
          <sphereGeometry />
          <meshStandardMaterial color="red" />
          <axesHelper scale={5} />
        </mesh>
      </mesh>
    </>
  );
};

export default MyElement3D;
```

[그림1]

[그림2]

<br>
<br>

## <mark class="pink">📌지오메트리</mark>

**Geometry**  
mesh의 모양을 결정한다.  
BufferGeometry라는 클래스를 상속 받는다.

**Position**  
형상을 정의하는 3차원 정점

**Normal**  
모델의 면에 대한 수직 벡터

**Color**  
정점의 색상

**UV**  
텍스쳐 맵핑을 위한 좌표

**Vertex Index**  
삼각형 면 구성을 위한 정점 인덱스

<br>

**Geometry 만드는 방법**

1\. mesh 사용

```jsx
<mesh>
  <boxGeometry />
  <meshStandardMaterial color="#1abc9c" />
</mesh>
```

<br>

2\. Drei 라이브러리 사용

Drei는 R3F에서 사용할 수 있는 유용한 컴포넌트들을 모아놓은 라이브러리이다.

```jsx
<Box position={[1.2, 0, 0]}>
  <meshStandardMaterial color="#8e44ad" />
</Box>
```

<br>

3\. THREE 라이브러리 사용

```jsx
import { Box, OrbitControls } from "@react-three/drei";
import * as THREE from "three";

const MyBox = (props) => {
  const geom = new THREE.BoxGeometry();
  return <mesh {...props} geometry={geom} />;
};

const MyElement3D = () => {
  return (
    <>
      <OrbitControls />

      <ambientLight intensity={0.1} />
      <directionalLight position={[2, 1, 3]} intensity={0.5} />

      <MyBox position={[-1.2, 0, 0]}>
        <meshStandardMaterial color="#e74c3c" />
      </MyBox>
    </>
  );
};

export default MyElement3D;
```

<br>

**두 개 mesh 하나로 합치기**

[원래 코드]

```jsx
import { Box, OrbitControls } from "@react-three/drei";
import * as THREE from "three";

const MyElement3D = () => {
  return (
    <>
      <OrbitControls />

      <ambientLight intensity={0.1} />
      <directionalLight position={[2, 1, 3]} intensity={0.5} />

      <mesh>
        <boxGeometry />
        <meshStandardMaterial color="#1abc9c" />
      </mesh>

      <mesh>
        <boxGeometry />
        <meshStandardMaterial emissive="yellow" wireframe={true} />
      </mesh>
    </>
  );
};

export default MyElement3D;
```

[수정한 코드]

ref를 사용하여 두 개의 geometry를 연결하면 된다.

```jsx
import { Box, OrbitControls } from "@react-three/drei";
import { useEffect, useRef } from "react";
import * as THREE from "three";

const MyElement3D = () => {
  const refMesh = useRef();
  const refWireMesh = useRef();

  useEffect(() => {
    refWireMesh.current.geometry = refMesh.current.geometry;
  }, []);

  return (
    <>
      <OrbitControls />

      <ambientLight intensity={0.1} />
      <directionalLight position={[2, 1, 3]} intensity={0.5} />

      <mesh ref={refMesh}>
        <boxGeometry args={[1, 1, 1, 1, 1, 1]} />
        <meshStandardMaterial color="#1abc9c" />
      </mesh>

      <mesh ref={refWireMesh}>
        <boxGeometry />
        <meshStandardMaterial emissive="yellow" wireframe={true} />
      </mesh>
    </>
  );
};

export default MyElement3D;
```

<br>

**지오매트리 args 변경하기**

leva 설치

```bash
npm i leva
```

<br>

useControls로 각 속성을 조정한다. useEffect deps에 속성들을 다 넣어주어야 두 개의 mesh가 계속 같이 움직인다.  
segment step은 정수만 가능하다.

```jsx
import { Box, OrbitControls } from "@react-three/drei";
import { useControls } from "leva";
import { useEffect, useRef } from "react";
import * as THREE from "three";

const MyElement3D = () => {
  const refMesh = useRef();
  const refWireMesh = useRef();

  const { xSize, ySize, zSize, xSegments, ySegments, zSegments } = useControls({
    xSize: { value: 1, min: 0.1, max: 5, step: 0.01 },
    ySize: { value: 1, min: 0.1, max: 5, step: 0.01 },
    zSize: { value: 1, min: 0.1, max: 5, step: 0.01 },
    xSegments: { value: 1, min: 1, max: 10, step: 1 },
    ySegments: { value: 1, min: 1, max: 10, step: 1 },
    zSegments: { value: 1, min: 1, max: 10, step: 1 },
  });

  useEffect(() => {
    refWireMesh.current.geometry = refMesh.current.geometry;
  }, [xSize, ySize, zSize, xSegments, ySegments, zSegments]);

  return (
    <>
      <OrbitControls />

      <ambientLight intensity={0.1} />
      <directionalLight position={[2, 1, 3]} intensity={0.5} />

      <mesh ref={refMesh}>
        <boxGeometry
          args={[xSize, ySize, zSize, xSegments, ySegments, zSegments]}
        />
        <meshStandardMaterial color="#1abc9c" />
      </mesh>

      <mesh ref={refWireMesh}>
        <boxGeometry />
        <meshStandardMaterial emissive="yellow" wireframe={true} />
      </mesh>
    </>
  );
};

export default MyElement3D;
```

[그림3]

<br>
<br>

## <mark class="pink">📌재질</mark>

```jsx
<meshPhongMaterial
  visible={true}
  transparent={true}
  opacity={0.5}
  depthTest={true}
  depthWrite={true}
  side={THREE.FrontSide}
  color={0xff0000}
  emissive={0x000000}
  specular={0xffff00}
  shineiness={100}
  flatShading={true}
  wireframe={false}
/>
```

```jsx
<meshStandardMaterial
  visible={true}
  transparent={true}
  opacity={1}
  depthTest={true}
  depthWrite={true}
  side={THREE.FrontSide}
  color={0xff0000}
  emissive={0x000000}
  roughness={roughness} // 거칠기
  metalness={metalness} // 금속성
  flatShading={true}
  wireframe={false}
/>
```

```jsx
<meshPhysicalMaterial
  visible={true}
  transparent={true}
  opacity={1}
  depthTest={true}
  depthWrite={true}
  side={THREE.FrontSide}
  color={0xff0000}
  emissive={0x000000}
  roughness={roughness}
  metalness={metalness}
  flatShading={false}
  wireframe={false}
  clearcoat={clearcoat} //코팅
  clearcoatRoughness={clearcoatRoughness} //코팅 거칠기
  transmission={transmission} //유리 투명도
  thickness={thickness} //유리 두께
  ior={ior} //굴절률
/>
```

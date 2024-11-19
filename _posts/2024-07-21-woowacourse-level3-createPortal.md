---
layout: single
title: "[우테코]Level3 createPortal"
categories: woowacourse
toc: true
toc_sticky: true
---

# createPortal을 이용해 모달 만들기

## <mark class="pink">📌createPortal이란?</mark>

- 일부 자식들을 DOM의 다른 부분으로 렌더링할 수 있는 기능을 제공합니다.
- 이벤트 버블링 문제를 해결할 수 있습니다.
- z-index 관리가 편합니다.

<br>
<br>

## <mark class="pink">📌createPortal로 모달 만들기</mark>

**index.html**

모달이 root에 있는 요소들 보다 위에 있어야 하므로 root 다음 줄에 modal 요소를 생성해줍니다.

```html
<body class="darkmode lightmode">
  <div id="root"></div>
  <div id="modal"></div>
  <script type="module" src="/src/main.tsx"></script>
</body>
```

<br>

**Modal.tsx**

```tsx
import React, { MouseEvent, useEffect } from "react";
import { createPortal } from "react-dom";
import * as S from "@/components/common/modal/Modal.style";

const portalElement = document.getElementById("modal") as HTMLElement;

interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  children?: React.ReactNode;
}

const Modal = ({ isOpen, onClose, children }: ModalProps) => {
  useEffect(() => {
    document.body.style.overflow = isOpen ? "hidden" : "auto";
  }, [isOpen]);

  if (!isOpen) return null;

  const handleModalContainerClick = (event: MouseEvent<HTMLDivElement>) => {
    event.stopPropagation();
  };

  return createPortal(
    <>
      <S.BackDrop onClick={onClose} />
      <S.ModalContent onClick={handleModalContainerClick}>
        {children}
      </S.ModalContent>
    </>,
    portalElement
  );
};

export default Modal;
```

<br>

**사용예시**

```tsx
import React, { useState } from "react";
import Modal from "./components/common/Modal";

const App = () => {
  const [isModalOpen, setIsModalOpen] = useState(false);

  return (
    <div>
      <button onClick={() => setIsModalOpen(true)}>Open Modal</button>
      <Modal isOpen={isModalOpen}>
        <div>
          <h2>모달 제목</h2>
          <p>모달 내용</p>
          <button onClick={() => setIsModalOpen(false)}>Close Modal</button>
        </div>
      </Modal>
    </div>
  );
};

export default App;
```

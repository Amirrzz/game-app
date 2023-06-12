<template>
  <div class="sizes">
    <header class="intro" id="startPage" v-show="!gameIsPlayed">
      <div class="game-container intro-container scaleInOutAnimation">
        <h1>Cubes Game</h1>
        <Loading />
        <div class="input-container">
          <Input
            label="Row:"
            v-model="boards[currentBoardIndex].rowInputCubes"
            :disabled="boards[currentBoardIndex].gameIsPlayed"
          />
          <Input
            label="Column:"
            v-model="boards[currentBoardIndex].columnInputCubes"
            :disabled="boards[currentBoardIndex].gameIsPlayed"
          />
          <Input
            label="Cube Size (px):"
            v-model="boards[currentBoardIndex].sizeOfCube"
            :disabled="boards[currentBoardIndex].gameIsPlayed"
          />

          <!-- <Input label="Gap Size (px):" v-model="gapOfCubesSize" /> -->
        </div>
        <div
          class="preview-board-container"
          v-for="(board, boardIndex) in boards"
          :key="'preview-board' + boardIndex"
          @click="setCurrenBoardIndex(boardIndex)"
          :style="{
            'align-items': board.boardIsBiggerThanViewPort ? 'unset' : 'center',
          }"
        >
          <div
            class="preview-board-info"
            :class="{ 'active-setting-board': boardIndex == currentBoardIndex }"
          >
            <span>Board:{{ boardIndex + 1 }}</span>
            <span> ~{{ board.sizeOfCube }} px</span>

            <span
              >{{ board.rowInputCubes }} X {{ board.columnInputCubes }}</span
            >
            <button class="button" v-show="!board.gameIsPlayed">
              <a
                :href="'#board-ref-' + boardIndex"
                @click="initGame(boardIndex)"
              ></a>
              Start
            </button>
            <span v-show="board.gameIsPlayed"> Started </span>
          </div>
          <div
            class="preview-cube-size-container"
            :style="{
              '--gap-size': +gapOfCubesSize + 'px',
              '--cubes-per-row': +boards[boardIndex].rowInputCubes,
              '--cube-size': +boards[boardIndex].sizeOfCube + 'px',
              '--row-size':
                (+boards[boardIndex].sizeOfCube + +gapOfCubesSize) *
                  +boards[boardIndex].rowInputCubes +
                'px',
            }"
          >
            <div
              v-for="(preViewCubeBoard, index) in +board.rowInputCubes"
              :key="index + 'previewCubeIndex'"
              class="preview-cube-item cube-item"
            >
              {{ preViewCubeBoard }}
            </div>
          </div>
        </div>
        <div class="buttons-container">
          <button class="button" @click="addBoard">Add Board</button>
        </div>
      </div>
    </header>

    <main
      class="container"
      v-for="(board, boardIndex) in boards"
      v-show="board.gameIsPlayed"
      :key="'board-container' + boardIndex"
      :style="{
        '--gap-size': +gapOfCubesSize + 'px',
        '--cubes-per-row': +boards[boardIndex].rowInputCubes,
        '--cube-size': +boards[boardIndex].sizeOfCube + 'px',
        '--row-size':
          (+boards[boardIndex].sizeOfCube + +gapOfCubesSize) *
            +boards[boardIndex].rowInputCubes +
          'px',
        'align-items': board.boardIsBiggerThanViewPort ? 'unset' : 'center',
      }"
    >
      <button class="button scaleInAnimation" :id="'board-ref-' + boardIndex">
        <a href="#startPage" id="restButton"></a>
        Board - {{ boardIndex + 1 }}
      </button>

      <section
        class="game-container scaleInAnimation"
        :class="[`board-${boardIndex}`]"
      >
        <div
          class="cube-item"
          v-for="(cube, index) in board.randomNumbers"
          :key="boardIndex + 'cube'"
          :class="{
            emptyCube: cube == board.randomNumbers.length,
            finishGamePass: board.gamePassed,
          }"
          :position="index + 1"
          :value="cube"
          style="transform: translate(0, 0)"
        >
          {{ cube }}
        </div>
      </section>
    </main>
  </div>
</template>

<script setup>
const gapOfCubesSize = ref(10);
function checkBoardIsBiggerThanViewPort(boardIndex) {
  const targetBoard = boards[boardIndex];
  const boardViewPortSize =
    (+targetBoard.sizeOfCube + +gapOfCubesSize.value) *
    +targetBoard.rowInputCubes;
  if (boardViewPortSize > +document.body.clientWidth - 20) {
    targetBoard.boardIsBiggerThanViewPort = true;
  } else {
    targetBoard.boardIsBiggerThanViewPort = false;
  }
}

const currentBoardIndex = ref(0);
const boards = reactive([
  {
    rowInputCubes: 4,
    columnInputCubes: 4,
    randomNumbers: [],
    checkPassGameNumbers: [],
    gameIsPlayed: false,
    gamePassed: false,
    sizeOfCube: 0,
    boardIsBiggerThanViewPort: false,
  },
]);

const addBoard = () => {
  boards.push({
    rowInputCubes: 4,
    columnInputCubes: 4,
    randomNumbers: [],
    checkPassGameNumbers: [],
    gameIsPlayed: false,
    gamePassed: false,
    sizeOfCube: 0,
    boardIsBiggerThanViewPort: false,
  });
  setPreviewCubeSize(boards.length - 1);
};
function setCurrenBoardIndex(index) {
  currentBoardIndex.value = index;
}

const generateRandomNumbers = (boardIndex = currentBoardIndex.value) => {
  try {
    const generateFakeNumber = false;

    const targetBoard = boards[boardIndex];
    const cubesLength =
      +targetBoard.rowInputCubes * +targetBoard.columnInputCubes;
    let tempArrayNumber = [];
    if (!generateFakeNumber) {
      targetBoard.checkPassGameNumbers = [];
      while (tempArrayNumber.length < cubesLength) {
        let r = Math.floor(Math.random() * cubesLength) + 1;
        if (tempArrayNumber.indexOf(r) === -1) tempArrayNumber.push(r);
      }
      targetBoard.randomNumbers = tempArrayNumber;
      targetBoard.checkPassGameNumbers = tempArrayNumber;
    } else {
      console.log("hey");
      for (let index = 1; index <= cubesLength; index++) {
        tempArrayNumber.push(index);
        console.log(cubesLength, index);
      }
      targetBoard.randomNumbers = tempArrayNumber;
      targetBoard.checkPassGameNumbers = tempArrayNumber;
    }
  } catch (error) {
    console.log(error);
  }
};
const resetCubesStates = (boardIndex = currentBoardIndex.value) => {
  const nodes = document.querySelectorAll(
    `.board-${boardIndex} .cube-item.active-cube`
  );
  nodes.forEach((e) => {
    e.onclick = null;
    e.classList.remove("active-cube");
  });
};
function setPreviewCubeSize(
  boardIndex = currentBoardIndex.value,
  initalDevide = 2.5
) {
  const targetBoard = boards[boardIndex];
  if (!targetBoard) return;
  if (+targetBoard.rowInputCubes < 1) {
    targetBoard.rowInputCubes = 0;
    return;
  }
  const size = (
    (+document.body.clientWidth - gapOfCubesSize.value * 2) /
      +targetBoard.rowInputCubes -
    gapOfCubesSize.value
  ).toFixed(2);
  if (initalDevide) {
    targetBoard.sizeOfCube = +size / initalDevide;
  } else {
    targetBoard.sizeOfCube = +size;
  }
}
function checkBoardsViewPort() {
  boards.forEach((e, i) => checkBoardIsBiggerThanViewPort(i));
}
onMounted(() => {
  window.addEventListener("resize", setPreviewCubeSize, true);
  window.addEventListener("resize", checkBoardsViewPort, true);
  setPreviewCubeSize();
});
const initGame = (boardIndex = currentBoardIndex.value) => {
  const targetBoard = boards[boardIndex];
  targetBoard.gameIsPlayed = true;
  generateRandomNumbers(boardIndex);
  resetCubesStates(boardIndex);
  setTimeout(() => {
    detectCubeItemSize(boardIndex);
  }, 1100);
};

const detectCubeItemSize = (boardIndex = currentBoardIndex.value) => {
  nextTick(() => {
    const cuber = document.querySelector(`.board-${boardIndex} .emptyCube`);

    const cuberPosition = +cuber.getAttribute("position");
    checkBoardIsBiggerThanViewPort(boardIndex);
    detectCubesMoveState(cuberPosition, boardIndex);
  });
};

const detectCubesMoveState = (
  cuberPosition,
  boardIndex = currentBoardIndex.value
) => {
  try {
    const targetBoard = boards[boardIndex];
    const row = +targetBoard.rowInputCubes;
    const column = +targetBoard.columnInputCubes;
    const biggerNumber = Math.max(row, column);
    const leftCube =
      cuberPosition % row == 1
        ? null
        : document.querySelector(
            `.board-${boardIndex} [position="${cuberPosition - 1}"]`
          );
    const rightCube =
      cuberPosition % row == 0
        ? null
        : document.querySelector(
            `.board-${boardIndex} [position="${cuberPosition + 1}"]`
          );
    const upCube =
      cuberPosition / row < 1
        ? null
        : document.querySelector(
            `.board-${boardIndex} [position="${cuberPosition - row}"]`
          );
    const downCube =
      cuberPosition / biggerNumber > biggerNumber - 1
        ? null
        : document.querySelector(
            `.board-${boardIndex} [position="${cuberPosition + row}"]`
          );

    if (leftCube) {
      leftCube.classList.add("active-cube");
      leftCube.onclick = function () {
        moveCubeHorizontal(this, "left", +targetBoard.sizeOfCube, boardIndex);
      };
    }
    if (rightCube) {
      rightCube.classList.add("active-cube");
      rightCube.onclick = function () {
        moveCubeHorizontal(this, "right", +targetBoard.sizeOfCube, boardIndex);
      };
    }

    if (upCube) {
      upCube.classList.add("active-cube");
      upCube.onclick = function () {
        moveCubeVertical(this, "up", +targetBoard.sizeOfCube, boardIndex);
      };
    }
    if (downCube) {
      downCube.classList.add("active-cube");
      downCube.onclick = function () {
        moveCubeVertical(this, "down", +targetBoard.sizeOfCube, boardIndex);
      };
    }
  } catch (e) {}
};

const regex = /.*?translate\((.*?), (.*?)\)/;
// regex:For Getting Position of cube item From it`s style value
const moveCubeHorizontal = (
  element,
  targetCubeDirection,
  targetCubeSize,
  boardIndex
) => {
  const cuber = document.querySelector(`.board-${boardIndex} .emptyCube`);
  const elementStyle = element.getAttribute("style").match(regex);
  const cuberStyles = cuber.getAttribute("style").match(regex);
  if (targetCubeDirection == "left") {
    element.style = `transform: translate(${
      parseFloat(elementStyle[1]) + targetCubeSize + 10 + "px"
    }, ${elementStyle[2]})`;

    cuber.style = `transform: translate(${
      parseFloat(cuberStyles[1]) - targetCubeSize - 10 + "px"
    }, ${cuberStyles[2]})`;
  } else {
    element.style = `transform: translate(${
      parseFloat(elementStyle[1]) - targetCubeSize - 10 + "px"
    }, ${elementStyle[2]})`;

    cuber.style = `transform: translate(${
      parseFloat(cuberStyles[1]) + targetCubeSize + 10 + "px"
    }, ${cuberStyles[2]})`;
  }
  animationEnd(element, boardIndex);
};
const moveCubeVertical = (
  element,
  targetCubeDirection,
  targetCubeSize,
  boardIndex
) => {
  const cuber = document.querySelector(`.board-${boardIndex} .emptyCube`);
  const elementStyle = element.getAttribute("style").match(regex);
  const cuberStyles = cuber.getAttribute("style").match(regex);
  if (targetCubeDirection == "up") {
    element.style = `transform: translate(${elementStyle[1]}, ${
      parseFloat(elementStyle[2]) + targetCubeSize + 10 + "px"
    })`;
    cuber.style = `transform: translate( ${cuberStyles[1]},${
      parseFloat(cuberStyles[2]) - targetCubeSize - 10 + "px"
    })`;
  } else {
    element.style = `transform: translate(${elementStyle[1]}, ${
      parseFloat(elementStyle[2]) - targetCubeSize - 10 + "px"
    })`;
    cuber.style = `transform: translate( ${cuberStyles[1]},${
      parseFloat(cuberStyles[2]) + targetCubeSize + 10 + "px"
    })`;
  }
  animationEnd(element, boardIndex);
};
const animationEnd = (element, boardIndex) => {
  const cuber = document.querySelector(`.board-${boardIndex} .emptyCube`);
  const targetBoard = boards[boardIndex];

  const elementIndexInArray = +element.getAttribute("position") - 1;
  const cuberIndexInArray = +cuber.getAttribute("position") - 1;

  element.setAttribute("position", cuberIndexInArray + 1);
  cuber.setAttribute("position", elementIndexInArray + 1);

  const tempValueCuber = targetBoard.checkPassGameNumbers[cuberIndexInArray];
  const tempValueTargetElement =
    targetBoard.checkPassGameNumbers[elementIndexInArray];
  const cloneCheckNumber = JSON.parse(
    JSON.stringify(targetBoard.checkPassGameNumbers)
  );
  cloneCheckNumber[cuberIndexInArray] = tempValueTargetElement;
  cloneCheckNumber[elementIndexInArray] = tempValueCuber;
  targetBoard.checkPassGameNumbers = cloneCheckNumber;
  setTimeout(() => {
    if (checkIsDone(boardIndex)) {
      gameIsDone(boardIndex);
      resetCubesStates(boardIndex);
    } else {
      resetCubesStates(boardIndex);
      nextTick(() => {
        detectCubesMoveState(elementIndexInArray + 1, boardIndex);
      });
    }
  }, 10);
};

const checkIsDone = (boardIndex) =>
  boards[boardIndex].checkPassGameNumbers.every(
    (value, index) => value == index + 1
  );

const gameIsDone = (boardIndex) => {
  boards[boardIndex].gamePassed = true;
};
</script>

<style>
.emptyCube {
  background-color: transparent;
  font-size: 0;
}
.active-cube {
  /* background-color: rgb(14 174 207); */
  cursor: pointer;
  opacity: 1;
}
.active-cube:hover {
  opacity: 0.9;
}

.intro {
  min-height: 100vh;
  min-width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  color: white;
  background-color: #212524;
}
.intro-container {
  display: flex;
  height: 100%;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  padding: 50px 0;
  gap: 5vw;
  min-width: 80vw;
}

.input-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 20px;
}

.preview-cube-size-container {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: var(--gap-size);
  flex-wrap: wrap;
  width: var(--row-size);
}
.preview-board-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100%;
  overflow: auto;
}

.preview-board-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px 10px;
  background-color: #898989;
  border-radius: 2px;
  margin-bottom: 10px;
  font-size: 18px;
  width: 100%;
  transition: 0.5s;
  cursor: pointer;
  flex-wrap: wrap;
  gap: 20px;
}
.preview-board-info .button {
  max-width: max-content;
  font-size: unset;
}
.active-setting-board {
  background-color: #26b7e7;
  cursor: default;
}
.buttons-container {
  display: flex;
  justify-content: center;
  gap: 10px;
  width: 100%;
}
</style>

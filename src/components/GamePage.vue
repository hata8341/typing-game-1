<script setup lang="ts">
import { ref } from "vue";
import { codeStore } from "../stores/code";
import { timerStore } from "../stores/timer";
import { userStore } from "../stores/user";
import { soundStore } from "../stores/sound";
import WinDialog from "../components/WinDialog.vue";

const user = userStore();
const code = codeStore();
const timer = timerStore();
const sound = soundStore();
const { setMisses, setGameFalse } = user;
const { startTimer, stopTimer } = timer;
const { moveIndex, startGame, setMissCount, changeLine } = code;
const keyboard = ref(null);
const upper = ref(null);
let showMyCodeDialog = ref<boolean>(false);
const openDialog = (): void => {
  showMyCodeDialog.value = true;
};
const closeDialog = (): void => {
  showMyCodeDialog.value = false;
};

//キーボードのhashmap
const keys: { [name: string]: string } = {};

//1
keys["1"] = "num1";
keys["2"] = "num2";
keys["3"] = "num3";
keys["4"] = "num4";
keys["5"] = "num5";
keys["6"] = "num6";
keys["7"] = "num7";
keys["8"] = "num8";
keys["9"] = "num9";
keys["0"] = "num0";
keys["-"] = "bar";
keys["^"] = "hat";
keys["¥"] = "¥";
keys["Backspace"] = "Backspace";
//uppercase
keys["!"] = "num1";
keys['"'] = "num2";
keys["#"] = "num3";
keys["$"] = "num4";
keys["%"] = "num5";
keys["&"] = "num6";
keys["'"] = "num7";
keys["("] = "num8";
keys[")"] = "num9";
keys["="] = "bar";
keys["~"] = "hat";
keys["|"] = "¥";

//2
keys["q"] = "q";
keys["w"] = "w";
keys["e"] = "e";
keys["r"] = "r";
keys["t"] = "t";
keys["y"] = "y";
keys["u"] = "u";
keys["i"] = "i";
keys["o"] = "o";
keys["p"] = "p";
keys["@"] = "at-mark";
keys["["] = "big-para-ini";
keys["]"] = "big-para-fini";
//uppercase
keys["Q"] = "q";
keys["W"] = "w";
keys["E"] = "e";
keys["R"] = "r";
keys["T"] = "t";
keys["Y"] = "y";
keys["U"] = "u";
keys["I"] = "i";
keys["O"] = "o";
keys["P"] = "p";
keys["`"] = "at-mark";
keys["{"] = "big-para-ini";
keys["}"] = "big-para-fini";

//3
keys["Control"] = "Control";
keys["a"] = "a";
keys["s"] = "s";
keys["d"] = "d";
keys["f"] = "f";
keys["g"] = "g";
keys["h"] = "h";
keys["j"] = "j";
keys["k"] = "k";
keys["l"] = "l";
keys[";"] = "semi";
keys[":"] = "coron";
keys["Enter"] = "Enter";
//uppercase
keys["A"] = "a";
keys["s"] = "s";
keys["D"] = "d";
keys["F"] = "f";
keys["G"] = "g";
keys["H"] = "h";
keys["J"] = "j";
keys["K"] = "k";
keys["L"] = "l";
keys["+"] = "semi";
keys["*"] = "coron";

//4
keys["Shift"] = "Shift";
keys["z"] = "z";
keys["x"] = "x";
keys["c"] = "c";
keys["v"] = "v";
keys["b"] = "b";
keys["n"] = "n";
keys["m"] = "m";
keys[","] = "hai";
keys["."] = "peri";
keys["/"] = "slash";
keys["_"] = "sub";
//4 uppercase
keys["Z"] = "z";
keys["X"] = "x";
keys["C"] = "c";
keys["V"] = "v";
keys["B"] = "b";
keys["N"] = "n";
keys["M"] = "m";
keys["<"] = "hai";
keys[">"] = "peri";
keys["?"] = "slash";

//5
keys["Alt"] = "Alt";
keys["Meta"] = "Meta";
keys[" "] = "space";

const KeyDown = () => {
  if (keys[event.key]) {
    keyboard.value
      .querySelectorAll("." + keys[event.key])[0]
      .classList.remove("bg-gray-100");
    keyboard.value
      .querySelectorAll("." + keys[event.key])[0]
      .classList.add("bg-indigo-500");
  }
  if (event.shiftKey) {
    if (keys[event.key]) {
      keyboard.value
        .querySelectorAll("." + keys[event.key])[0]
        .classList.remove("bg-gray-100");
      keyboard.value
        .querySelectorAll("." + keys[event.key])[0]
        .classList.add("bg-indigo-500");
    }
  }
  //スタート
  if (code.correctCode === "" && event.key === " ") {
    startGame();
    startTimer();
  }
  //ポインターとキーがあっているか
  else if (event.key === code.pointerCode) {
    moveIndex();
    sound.onSuccess();
    if (code.finishCode.length + 1 === code.index) {
      stopTimer();
      setGameFalse();
      if (keys[event.key]) {
        keyboard.value
          .querySelectorAll("." + keys[event.key])[0]
          .classList.remove("bg-indigo-500");
        keyboard.value
          .querySelectorAll("." + keys[event.key])[0]
          .classList.add("bg-gray-100");
      }
      keyboard.value
        .querySelectorAll(".Shift")[0]
        .classList.remove("bg-indigo-500");
      keyboard.value.querySelectorAll(".Shift")[0].classList.add("bg-gray-100");
      console.log("color dismissed");
      openDialog();
      return;
    }
    if (code.pointerCode === "\n") {
      changeLine();
    }
  }
  //3: shiftの時、ポインターと打ったキーが同じかどうかの判定
  else if (event.shiftKey) {
    if (event.key === code.pointerCode) {
      moveIndex();

      if (code.finishCode.length + 1 === code.index) {
        stopTimer();
        keyboard.value
          .querySelectorAll(".Shift")[0]
          .classList.remove("bg-indigo-500");
        keyboard.value
          .querySelectorAll(".Shift")[0]
          .classList.add("bg-gray-100");
        setGameFalse();
        openDialog();
        return;
      }
      if (code.pointerCode === "\n") {
        changeLine();
      }
    }
  } else {
    setMissCount();
    setMisses(event.key);
    user.setScore();
    sound.onMiss();
    console.log("you clicked wrong key");
  }
};

const KeyUp = () => {
  if (keys[event.key]) {
    keyboard.value
      .querySelectorAll("." + keys[event.key])[0]
      .classList.remove("bg-indigo-500");
    keyboard.value
      .querySelectorAll("." + keys[event.key])[0]
      .classList.add("bg-gray-100");
  }
  if (event.shiftKey) {
    if (keys[event.key]) {
      keyboard.value
        .querySelectorAll("." + keys[event.key])[0]
        .classList.remove("bg-indigo-500");
      keyboard.value
        .querySelectorAll("." + keys[event.key])[0]
        .classList.add("bg-gray-100");
    }
  }
};
//ページ全体を開いている時にどこを押してもkeyeventが起こる
document.onkeydown = () => {
  if (user.canStartGame) {
    KeyDown();
  }
  return;
};
document.onkeyup = () => {
  if (user.canStartGame) {
    KeyUp();
  }
  return;
};
</script>

<template>
  <!-- 上半分のHTML -->
  <div
    ref="upper"
    class="upper-box mt-2 mb-2 bg-white flex justify-around items-center"
  >
    <div class="code-area overs flex justify-center items-center">
      <pre
        class="code-area p-5"
      ><code class="language-html"><span id="correct">{{code.correctCode}}</span><span id="that">{{code.pointerCode}}</span>{{code.notYetCode}}</code></pre>
    </div>
  </div>

  <!--キーボードの設計
    - 縦五行である。
      - １４(割合: 13が同サイズ、一番右約1.6倍)
      - １４(割合: 13が同サイズ、一番ひだり約1.5倍...これは見た目の調整的に1.6ではなく1.5）
      - １３(割合: 11が同じ、残り２が1.7倍）
      - １２(割合: 10が同じ、残り２が1.8倍）
      - ５(横970pxを10等分。97px 1:2:4:2;1 => 97:194:388:194:97px)

    - 下半分の画面のサイズが338pxなのでpaddingをのぞいて330pxとする
    - 一個66pxとなる

      横が978px
        66 * 13 = 858px
        112 = 970px
        978px

      たてが338px
        - padding 8
        - 330px
      - 330 / 5 = 66px

      10
      12421

    以下キーボードのHTML-->

  <div class="bottom-box flex justify-center items-center bg-gray-900">
    <div ref="keyboard" class="board-area bg-gray-200 p-1">
      <!-- １行目 -->
      <div class="one-line flex">
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="num1 bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>!</div>
            <div>1</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="num2 bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>"</div>
            <div>2</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="num3 bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>#</div>
            <div>3</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="num4 bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>$</div>
            <div>4</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="num5 bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>%</div>
            <div>5</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="num6 bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>&amp;</div>
            <div>6</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="num7 bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>'</div>
            <div>7</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="num8 bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>(</div>
            <div>8</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="num9 bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>)</div>
            <div>9</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="num0 bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>0</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="bar bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>=</div>
            <div>-</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="hat bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>^</div>
            <div>~</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="¥ bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>¥</div>
            <div>|</div>
          </div>
        </div>
        <div class="bg-gray-600 one-six flex justify-center items-center">
          <div
            class="Backspace bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons"
          >
            <div>
              <br />
            </div>
            <div>backspace</div>
          </div>
        </div>
      </div>

      <!-- 2行目 -->
      <div class="one-line flex">
        <div class="bg-gray-600 one-five flex justify-center items-center">
          <div class="tabs bg-gray-100 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div></div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="q bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>Q</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="w bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>W</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="e bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>E</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="r bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>R</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="t bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>T</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="y bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>Y</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="u bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>U</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="i bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>I</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="o bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>O</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="p bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>P</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div
            class="at-mark bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons"
          >
            <div>`</div>
            <div>@</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div
            class="big-para-ini bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons"
          >
            <div>{</div>
            <div>[</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div
            class="big-para-fini bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons"
          >
            <div>}</div>
            <div>]</div>
          </div>
        </div>
      </div>

      <!-- 3行目 -->
      <div class="one-line flex">
        <div class="bg-gray-600 one-seven flex justify-center items-center">
          <div
            class="Control bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons"
          >
            <div>
              <br />
            </div>
            <div>control</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="a bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>A</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="s bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>S</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="d bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>D</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="f bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>F</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="g bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>G</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="h bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>H</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="j bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>J</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="k bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>K</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="l bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>L</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="semi bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>+</div>
            <div>;</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="coron bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>*</div>
            <div>:</div>
          </div>
        </div>
        <div class="bg-gray-600 one-seven flex justify-center items-center">
          <div class="Enter bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>Enter</div>
          </div>
        </div>
      </div>

      <!-- 4行目 -->
      <div class="one-line flex">
        <div
          class="bg-gray-600 buttons one-five flex justify-center items-center"
        >
          <div class="Shift bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>Shift</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="z bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>Z</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="x bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>x</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="c bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>C</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="v bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>V</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="b bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>B</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="n bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>N</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="m bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>M</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="hai bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>&lt;</div>
            <div>,</div>
            &gt;
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="peri bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>&gt;</div>
            <div>.</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="slash bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>?</div>
            <div>/</div>
          </div>
        </div>
        <div class="bg-gray-600 buttons flex justify-center items-center">
          <div class="sub bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>_</div>
          </div>
        </div>
        <div class="bg-gray-600 one-eight flex justify-center items-center">
          <div class="bg-gray-100 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div></div>
          </div>
        </div>
      </div>

      <!-- 5行目 -->
      <div class="one-line flex">
        <div class="bg-gray-600 one-four flex justify-center items-center">
          <div class="Alt bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>option</div>
          </div>
        </div>
        <div class="bg-gray-600 one-ten flex justify-center items-center">
          <div class="Meta bg-gray-100 hover:bg-indigo-400 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div>command</div>
          </div>
        </div>
        <div
          id="space"
          class="bg-gray-600 space-bar flex justify-center items-center"
        >
          <div
            class="space bg-gray-100 p-2 hover:bg-indigo-400 inner-buttons"
          ></div>
        </div>
        <div class="bg-gray-600 one-ten flex justify-center items-center">
          <div class="Meta bg-gray-100 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div></div>
          </div>
        </div>
        <div class="bg-gray-600 one-four flex justify-center items-center">
          <div class="bg-gray-100 p-2 inner-buttons">
            <div>
              <br />
            </div>
            <div></div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <WinDialog :showMyCodeDialog="showMyCodeDialog" @closeDialog="closeDialog" />
</template>
<style scoped>
.mass {
  width: 100%;
}
.upper-box {
  height: 45%;
  margin: 0;
}
.code-area {
  width: 80%;
  height: 100%;
}
.bottom-box {
  width: 100%;
  height: 55%;
}
.board-area {
  width: 978px;
  height: 338px;
}
.one-line {
  height: 66px;
  width: 100%;
}
.buttons {
  width: 66px;
  height: 66px;
}
.inner-buttons {
  width: 97%;
  height: 97%;
}
.one-four {
  width: 97px;
  height: 66px;
}
.one-five {
  width: 107px;
  height: 66px;
}
.one-six {
  width: 112px;
  height: 66px;
}
.one-seven {
  width: 122px;
  height: 66px;
}
.one-eight {
  width: 155px;
  height: 66px;
}
.one-ten {
  width: 194px;
  height: 66px;
}
.overs {
  overflow: scroll;
}
.space-bar {
  width: 400px;
  height: 66px;
}
.make-it {
  word-wrap: break-word;
}
textarea::selection {
  background: #fff;
  color: #ff0000;
}

#correct {
  color: #0000ff;
}
#that {
  animation: flash 1s linear infinite;
  background: #808080;
}
#wrong {
  color: #ff0000;
}
@keyframes flash {
  0%,
  100% {
    opacity: 1;
  }

  50% {
    opacity: 0;
  }
}
</style>

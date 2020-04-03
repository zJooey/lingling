<template>
  <div
    id="gamePanel"
    @touchstart="handleTouchStart"
    @touchend="handleTouchEnd"
    @touchmove="handleTouchMove"
  >
    <p><a id="newGame" class="button" @click="newGame">New Game</a> Score: <span id="score">{{ score }}</span></p>
    <div id="gridPanel">
      <div class="grid" />
      <div class="grid" />
      <div class="grid" />
      <div class="grid" />

      <div class="grid" />
      <div class="grid" />
      <div class="grid" />
      <div class="grid" />

      <div class="grid" />
      <div class="grid" />
      <div class="grid" />
      <div class="grid" />

      <div class="grid" />
      <div class="grid" />
      <div class="grid" />
      <div class="grid" />

      <!--  Demo Cell Style
        <div class="cell num2" id="cell00" >2</div>
        <div class="cell num4" id="cell01" >4</div>
        <div class="cell num8" id="cell02" >8</div>
        <div class="cell num16" id="cell03" >16</div>

        <div class="cell num32" id="cell10" >32</div>
        <div class="cell num64" id="cell11" >64</div>
        <div class="cell num128" id="cell12" >128</div>
        <div class="cell num256" id="cell13" >256</div>

        <div class="cell num512" id="cell20" >512</div>
        <div class="cell num1024" id="cell21" >1024</div>
        <div class="cell num2048" id="cell22" >2048</div>
        <div class="cell num4096" id="cell23" >4096</div>

        <div class="cell num8192" id="cell30" >8192</div>
        <div class="cell " id="cell31" > </div>
        <div class="cell " id="cell32" > </div>
        <div class="cell " id="cell33" > </div> -->

      <div id="cell00" class="cell" />
      <div id="cell01" class="cell" />
      <div id="cell02" class="cell" />
      <div id="cell03" class="cell" />

      <div id="cell10" class="cell num2">2</div>
      <div id="cell11" class="cell" />
      <div id="cell12" class="cell" />
      <div id="cell13" class="cell" />

      <div id="cell20" class="cell" />
      <div id="cell21" class="cell" />
      <div id="cell22" class="cell num4">4</div>
      <div id="cell23" class="cell" />

      <div id="cell30" class="cell" />
      <div id="cell31" class="cell" />
      <div id="cell32" class="cell" />
      <div id="cell33" class="cell" />

    </div>
    <div id="gameOver" style="display: none;">
      <div><!--背景 --></div>
      <p><!-- 前景 -->
        Game Over!<br>
        Score: <span id="finalScore">{{ score }}}</span><br>
        <a id="restart" class="button" @click="againGame">Try Again!</a>
      </p>
    </div>
  </div>
</template>

<script>
import animation from '@/utils/animation'
export default {
  name: 'Page1024',
  data() {
    return {
      // game 封装了2048的数据和核心算法
      cells: [[2, 0, 0, 0], [0, 32, 0, 0], [0, 4, 0, 0], [0, 0, 2048, 0]],
      // 游戏进行中
      PLAYING: 0,
      // 方块正在移动动画处理中，期间不能响应键盘事件
      CELL_MOVEING: 1,
      // 游戏结束了，结束了就不能响应键盘事件了
      GAME_OVER: 2,
      // 当前游戏状态
      state: this.PLAYING,
      // 分数
      score: 0,
      // 动态效果开关,打开后可以绘制方块的移动动画效果
      effect: true,
      startX: 0,
      startY: 0,
      endX: 0,
      endY: 0
    }
  },
  mounted() {
    this.load()
  },
  methods: {
    // 向上的动作
    upAction() {
      if (this.state === this.CELL_MOVEING) {
        return false
      }
      if (!this.canMoveUp()) {
        return false
      }
      // 每次处理一个列
      for (let col = 0; col < 4; col++) {
        // 每一个列中 从放方向判断是否需要移动处理
        this.upCol(col)
      }
      return true
    },
    // 处理一个列向上的移动
    upCol(col) {
      // 一个列中，按照方方向检查是否需要合并处理。
      for (let row = 0; row < 4;) {
        const current = this.cells[row][col]
        const nextRow = this.getNextInCol(col, row + 1, 1)
        // 没有下一个，就直接结束了
        if (nextRow === -1) {
          return
        }
        const next = this.cells[nextRow][col]
        let obj, top, left
        if (current === 0) {
          // 下一个格子移动到当前位置。
          this.cells[row][col] = next
          this.cells[nextRow][col] = 0
          if (this.effect) { // 如果动效开关打开，就处理动画
            // this.$("cell"+nextRow+col) 找到下一个对象对应的格子
            obj = this.$('cell' + nextRow + col)
            top = row * 120 + 20
            left = col * 120 + 20
            animation.add(obj, top, left)
          }
        } else if (current === next) {
          // 两个格子一样，就合并格子
          this.cells[row][col] = next + current
          this.cells[nextRow][col] = 0

          this.score += this.cells[row][col]

          row++
          if (this.effect) { // 如果动效开关打开，就处理动画
            // this.$("cell"+nextRow+col) 找到下一个对象对应的格子
            obj = this.$('cell' + nextRow + col)
            top = row * 120 + 20
            left = col * 120 + 20
            animation.add(obj, top, left)
          }
        } else {
          // 下个不一样，就忽略之
          row++
        }
      }
    },
    // 得到当前行或列的下一个有值的格子
    getNextInCol(col, startRow, step) {
      let row = startRow
      /* eslint-disable no-unmodified-loop-condition */
      /* eslint-enable no-unmodified-loop-condition */
      // 取消下一行的警告
      /* eslint-disable-next-line */
      while (true) {
        if (row < 0 || row >= 4) {
          return -1
        }
        if (this.cells[row][col] !== 0) {
          return row
        }
        row += step
      }
    },
    // 向下的动作
    downAction() {
      if (this.state === this.CELL_MOVEING) {
        return false
      }
      if (!this.canMoveDown()) {
        return false
      }
      // 每次处理一个列
      for (let col = 0; col < 4; col++) {
        // 每一个列中 从放方向判断是否需要移动处理
        this.downCol(col)
      }
      return true
    },
    // 处理一个列向下的移动
    downCol(col) {
    // 一个列中，按照方方向检查是否需要合并处理。
      for (let row = 3; row >= 0;) {
        const current = this.cells[row][col]
        const nextRow = this.getNextInCol(col, row - 1, -1)
        // 没有下一个，就直接结束了
        if (nextRow === -1) {
          return
        }
        const next = this.cells[nextRow][col]
        let obj, top, left
        if (current === 0) {
        // 下一个格子移动到当前位置。
          this.cells[row][col] = next
          this.cells[nextRow][col] = 0
          if (this.effect) { // 如果动效开关打开，就处理动画
          // this.$("cell"+nextRow+col) 找到下一个对象对应的格子
            obj = this.$('cell' + nextRow + col)
            top = row * 120 + 20
            left = col * 120 + 20
            animation.add(obj, top, left)
          }
        } else if (current === next) {
        // 两个格子一样，就合并格子
          this.cells[row][col] = next + current
          this.cells[nextRow][col] = 0

          this.score += this.cells[row][col]

          row--

          if (this.effect) { // 如果动效开关打开，就处理动画
          // this.$("cell"+nextRow+col) 找到下一个对象对应的格子
            obj = this.$('cell' + nextRow + col)
            top = row * 120 + 20
            left = col * 120 + 20
            animation.add(obj, top, left)
          }
        } else {
        // 下个不一样，就忽略之
          row--
        }
      }
    },
    // 向左的动作
    leftAction() {
      if (this.state === this.CELL_MOVEING) {
        return false
      }
      if (!this.canMoveLeft()) {
        return false
      }
      // 每次处理一个行
      for (let row = 0; row < 4; row++) {
        // 每一个行中 从放方向判断是否需要移动处理
        this.moveLeft(row)
      }
      return true
    },
    // 处理一个行向左的移动
    moveLeft(row) {
      // 一个行中，按照方方向检查是否需要合并处理。
      for (let col = 0; col < 4;) {
        const current = this.cells[row][col]
        const nextCol = this.getNextInRow(row, col + 1, 1)
        // 没有下一个，就直接结束了
        if (nextCol === -1) {
          return
        }
        const next = this.cells[row][nextCol]

        // console.log("next:"+next);
        // console.log("current:"+current);
        let obj, top, left
        if (current === 0) {
          // 下一个格子移动到当前位置。
          this.cells[row][col] = next
          this.cells[row][nextCol] = 0
          if (this.effect) { // 如果动效开关打开，就处理动画
            // this.$("cell"+nextRow+col) 找到下一个对象对应的格子
            obj = this.$('cell' + row + nextCol)
            top = row * 120 + 20
            left = col * 120 + 20
            animation.add(obj, top, left)
          }
        } else if (current === next) {
          // 两个格子一样，就合并格子
          this.cells[row][col] = next + current
          this.cells[row][nextCol] = 0

          this.score += this.cells[row][col]

          col++
          if (this.effect) { // 如果动效开关打开，就处理动画
            // this.$("cell"+nextRow+col) 找到下一个对象对应的格子
            obj = this.$('cell' + row + nextCol)
            top = row * 120 + 20
            left = col * 120 + 20
            animation.add(obj, top, left)
          }
        } else {
          // 下个不一样，就忽略之
          col++
        }
      }
    },
    getNextInRow(row, startCol, step) {
      var col = startCol
      // 取消下一行的警告
      /* eslint-disable-next-line */
      while (true) {
        if (col < 0 || col >= 4) {
          return -1
        }
        if (this.cells[row][col] !== 0) {
          return col
        }
        col += step
      }
    },
    // 向右的动作
    rightAction() {
      if (this.state === this.CELL_MOVEING) {
        return false
      }
      if (!this.canMoveRight()) {
        return false
      }
      // 每次处理一个行
      for (let row = 0; row < 4; row++) {
        // 每一个行中 从放方向判断是否需要移动处理
        this.moveRight(row)
      }
      return true
    },
    // 处理一个行向右的移动
    moveRight(row) {
      // 一个行中，按照方方向检查是否需要合并处理。
      for (let col = 3; col >= 0;) {
        const current = this.cells[row][col]
        const nextCol = this.getNextInRow(row, col - 1, -1)
        // 没有下一个，就直接结束了
        if (nextCol === -1) {
          return
        }
        const next = this.cells[row][nextCol]

        // console.log("next:"+next);
        // console.log("current:"+current);
        let obj, top, left
        if (current === 0) {
          // 下一个格子移动到当前位置。
          this.cells[row][col] = next
          this.cells[row][nextCol] = 0
          if (this.effect) { // 如果动效开关打开，就处理动画
            // this.$("cell"+nextRow+col) 找到下一个对象对应的格子
            obj = this.$('cell' + row + nextCol)
            top = row * 120 + 20
            left = col * 120 + 20
            animation.add(obj, top, left)
          }
        } else if (current === next) {
          // 两个格子一样，就合并格子
          this.cells[row][col] = next + current
          this.cells[row][nextCol] = 0

          this.score += this.cells[row][col]
          col--
          if (this.effect) { // 如果动效开关打开，就处理动画
            // this.$("cell"+nextRow+col) 找到下一个对象对应的格子
            obj = this.$('cell' + row + nextCol)
            top = row * 120 + 20
            left = col * 120 + 20
            animation.add(obj, top, left)
          }
        } else {
          // 下个不一样，就忽略之
          col--
        }
      }
    },
    // 判断是否可以上移
    canMoveUp() {
      for (let col = 0; col < 4; col++) {
        for (let row = 1; row < 4; row++) {
          // 格子上方是空位置, 可以移动
          if (this.cells[row][col] !== 0 && this.cells[row - 1][col] === 0) {
            return true
          }
          // 格子上方相邻的相等，可以移动
          if (this.cells[row][col] !== 0 && (this.cells[row][col] === this.cells[row - 1][col])) {
            return true
          }
        }
      }
      return false
    },
    // 判断是否可以下移
    canMoveDown() {
      for (let col = 0; col < 4; col++) {
        for (let row = 0; row < 3; row++) {
          // 格子上方是空位置, 可以移动
          if (this.cells[row][col] !== 0 && this.cells[row + 1][col] === 0) {
            return true
          }
          // 格子上方相邻的相等，可以移动
          if (this.cells[row][col] !== 0 && (this.cells[row][col] === this.cells[row + 1][col])) {
            return true
          }
        }
      }
      return false
    },
    // 判断是否可以左移
    canMoveLeft() {
      for (let col = 1; col < 4; col++) {
        for (let row = 0; row < 4; row++) {
          // 格子上方是空位置, 可以移动
          if (this.cells[row][col] !== 0 && this.cells[row][col - 1] === 0) {
            return true
          }
          // 格子上方相邻的相等，可以移动
          if (this.cells[row][col] !== 0 && (this.cells[row][col] === this.cells[row][col - 1])) {
            return true
          }
        }
      }
      return false
    },
    // 判断是否可以右移
    canMoveRight() {
      for (let col = 0; col < 3; col++) {
        for (let row = 0; row < 4; row++) {
          // 格子上方是空位置, 可以移动
          if (this.cells[row][col] !== 0 && this.cells[row][col + 1] === 0) {
            return true
          }
          // 格子上方相邻的相等，可以移动
          if (this.cells[row][col] !== 0 && (this.cells[row][col] === this.cells[row][col + 1])) {
            return true
          }
        }
      }
      return false
    },
    // 测试
    test() {
      this.rightAction()
      this.state = this.CELL_MOVEING
      animation.start(function() {
        // console.log("update");
        this.randomNumber()
        this.updateView()
        this.state = this.PLAYING
      })
    },
    // 更新显示，将表格中的数据，更新到界面显示
    updateView() {
      for (let row = 0; row < 4; row++) {
        for (let col = 0; col < 4; col++) {
          const n = this.cells[row][col]
          const cell = this.$('cell' + row + col)
          // 清楚显示的数据和显示样式
          cell.className = 'cell'
          cell.innerHTML = ''
          if (n > 0) {
            // 更新显示样式
            cell.className = 'cell num' + n
            // 更新显示的数字
            cell.innerHTML = n
          }
        }
      }
      // this.$('score').innerHTML = this.score
      // this.$('finalScore').innerHTML = this.score
    },
    // 检查当前的表格中是否是满的，如果满了返回true，否则返回false
    full() {
      for (let row = 0; row < 4; row++) {
        for (let col = 0; col < 4; col++) {
          if (this.cells[row][col] === 0) {
            return false
          }
        }
      }
      return true
    },
    // 向表格随机插入一个数字，如果插入成功返回true，插入失败返回false
    randomNumber() {
      if (this.full()) {
        return false
      }
      /* eslint-disable no-unmodified-loop-condition */
      /* eslint-enable no-unmodified-loop-condition */
      // 取消下一行的警告
      /* eslint-disable-next-line */
      while (true) {
        const col = parseInt(Math.random() * 4)
        const row = parseInt(Math.random() * 4)
        if (this.cells[row][col] === 0) {
          const n = Math.random() < 0.5 ? 2 : 4
          this.cells[row][col] = n
          return true
        }
      }
    },
    // 开始
    startAction() {
      this.$('gameOver').style.display = 'none'
      for (let row = 0; row < 4; row++) {
        for (let col = 0; col < 4; col++) {
          this.cells[row][col] = 0
        }
      }
      this.score = 0
      this.randomNumber()
      this.randomNumber()
      this.updateView()
      this.state = this.PLAYING
    },
    // 元素查询方法
    $(id) {
      return document.getElementById(id)
    },
    // 是否已经玩到8192了
    has8192() {
      for (let row = 0; row < 4; row++) {
        for (let col = 0; col < 4; col++) {
          if (this.cells[row][col] === 8192) {
            return true
          }
        }
      }
    },
    // 判断是否有空位子
    hasSpace() {
      for (let row = 0; row < 4; row++) {
        for (let col = 0; col < 4; col++) {
          if (this.cells[row][col] === 0) {
            return true
          }
        }
      }
    },
    // 显示游戏结束界面
    gameOver() {
      // 发现8192游戏结束
      if (this.has8192()) {
        this.state = this.GAME_OVER
        this.$('gameOver').style.display = 'block'
        return true
      }

      // 发现空位置，游戏不结束
      if (this.hasSpace()) {
        return false
      }

      // 能够移动游戏不结束
      if (this.canMoveUp() || this.canMoveDown() || this.canMoveLeft() || this.canMoveRight()) {
        return false
      }
      this.state = this.GAME_OVER
      this.$('gameOver').style.display = 'block'
      return true
    },
    // 新游戏
    newGame() {
      if (this.state === this.PLAYING) { this.startAction() }
    },
    // 重新开始
    againGame() {
      if (this.state === this.GAME_OVER) {
        this.startAction()
        this.score = 0
      }
    },
    // 初始化
    load() {
      const _this = this
      _this.startAction()
      // 监听键盘事件
      document.onkeydown = function(event) {
        if (_this.state !== _this.PLAYING) {
          return
        }
        let move = false
        switch (event.keyCode) {
          case 37:// left
            move = _this.leftAction()
            break
          case 38:// up
            move = _this.upAction()
            break
          case 39:// right
            move = _this.rightAction()
            break
          case 40:// down
            move = _this.downAction()
            break
        }
        if (!move) {
          return
        }
        _this.afterAction()
      }
    },
    // 移动之后的动作处理
    afterAction() {
      const _this = this
      if (_this.effect) {
        _this.state = _this.CELL_MOVEING
        animation.start(function() {
          // console.log("update");
          _this.updateView()
          _this.state = _this.PLAYING
          if (!_this.gameOver()) {
            setTimeout(function() {
              _this.randomNumber()
              _this.updateView()
            }, 100)
          }
        })
      } else {
        if (!_this.gameOver()) {
          setTimeout(function() {
            _this.randomNumber()
            _this.updateView()
          }, 100)
        }
        _this.updateView()
        _this.state = _this.PLAYING
      }
      _this.gameOver()
    },
    /**
     * clientX：触摸目标在视口中的x坐标。
     * clientY：触摸目标在视口中的y坐标。
     * identifier：标识触摸的唯一ID。
     * pageX：触摸目标在页面中的x坐标。
     * pageY：触摸目标在页面中的y坐标。
     * screenX：触摸目标在屏幕中的x坐标。
     * screenY：触摸目标在屏幕中的y坐标。
     * target：触目的DOM节点目标
     */
    handleTouchStart(e) {
      const _this = this
      _this.startX = e.touches[0].pageX
      _this.startY = e.touches[0].pageY
    },
    handleTouchEnd(e) {
      const _this = this
      _this.endX = e.changedTouches[0].pageX
      _this.endY = e.changedTouches[0].pageY
      let move
      if (Math.abs(_this.endX - _this.startX) > Math.abs(_this.endY - _this.startY)) {
        if (_this.endX > _this.startX) {
          move = _this.rightAction()
        } else {
          move = _this.leftAction()
        }
      } else {
        if (_this.endY > _this.startY) {
          move = _this.downAction()
        } else {
          move = _this.upAction()
        }
      }
      if (!move) {
        return
      }
      this.afterAction()
    },
    handleTouchMove(e) {
      // this.score = 10000
    }
  }
}
</script>

<style scoped>
  *{
    padding: 0;
    margin: 0;

  }

  div, p, h1, h2, a,span{
    font-family: Arial,serif;
    font-weight: bold;
  }
  header{
    padding-top: 16px;
  }
  h1{
    font-size: 40px;
    font-weight: bold;
    text-align: center;
  }
  #gamePanel{
    margin: 0 auto;
    width: 500px;
    position: relative;
  }
  #gridPanel{
    width: 500px;
    height: 500px;
    background: #BBADA0;
    border-radius: 10px;
    padding: 20px 0 0 20px;
    position: relative;
  }
  #gamePanel p{
    padding: 8px;
  }
  /* 按钮样式 */
  .button{
    display: inline-block;
    padding: 10px;
    background: #9F8B77;
    border-radius: 6px;
    color: #FFF;
    cursor: pointer;
  }
  .grid, .cell{
    width: 100px;
    height: 100px;
    border-radius: 6px;
  }
  /* 背景网格 */
  #gamePanel .grid{
    background-color: #ccc0b3;
    float: left;
    margin: 0 20px 20px 0;
  }
  /* 格式化前景单元格中位置 */
  .cell{
    position: absolute;
    line-height: 100px;
    vertical-align: middle;
    text-align: center;
    font-size: 60px;
    color: #776E65;
  }
  /* 前景格中的行位置 */
  #cell00, #cell01, #cell02, #cell03{
    top: 20px;
  }
  #cell10, #cell11, #cell12, #cell13{
    top: 140px;
  }
  #cell20, #cell21, #cell22, #cell23{
    top: 260px;
  }
  #cell30, #cell31, #cell32, #cell33{
    top: 380px;
  }
  /* 前景格中的列位置 */
  #cell00, #cell10, #cell20, #cell30{
    left: 20px;
  }
  #cell01, #cell11, #cell21, #cell31{
    left: 140px;
  }
  #cell02, #cell12, #cell22, #cell32{
    left: 260px;
  }
  #cell03, #cell13, #cell23, #cell33{
    left: 380px;
  }
  /* 数字显示效果 */
  .num8, .num16, .num32, .num64, .num128, .num256, .num512, .num1024, .num2048, .num4096, .num8192{
    color: #fff;
  }
  .num1024, .num2048, .num4096, .num8192{
    font-size: 40px;
  }
  .num2{
    background: #eee4da;
  }
  .num4{
    background: #ede0c8;
  }
  .num8{
    background: #f2b179;
  }
  .num16{
    background:#f59563;
  }
  .num32{
    background:#f67c5f;
  }
  .num64{
    background:#f65e3b;
  }
  .num128{
    background:#edcf72;
  }
  .num256{
    background:#edcc61;
  }
  .num512{
    background:#9c0;
  }
  .num1024{
    background:#33b5e5;
  }
  .num2048{
    background:#09c;
  }
  .num4096{
    background:#a6c;
  }
  .num8192{
    background:#93c;
  }
  /* 格式化Game Over的样式 */
  #gameOver{
    display: none;
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
  }
  #gameOver div{
    width: 100%;
    height: 100%;
    background: #555;
    filter:alpha(Opacity=50);
    -moz-opacity:0.5;
    opacity: 0.5;
  }
  #gameOver p{
    position: absolute;
    top: 150px;
    left: 100px;
    border-radius: 10px;
    width: 300px;
    border: 1px solid #EDCF72;
    background: #fff;
    line-height: 1.6em;
    font-size: 30px;
    color: #000;
    text-align: center;
  }
</style>

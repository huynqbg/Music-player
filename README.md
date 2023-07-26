1.Hạn chế tối đa các bài hát bị lặp lại
 currentIndex: 0,
        isPlaying: false,
        isRandom: false,
        isRepeat: false,
        playedIndexes: [],
thêm mảng playedIndexes[]
 loadConfig: function () {
          this.isRandom = this.config.isRandom
          this.isRepeat = this.config.isRepeat
          this.currentIndex = this.config.currentIndex
          this.playedIndexes.push(this.config.currentIndex)
          //Object.assign(this, this.config)
        },
Load bài hát hiện tại push vào mảng playedIndexes
playRandomSong: function () {
          let newIndex
          
          do {
            newIndex = Math.floor(Math.random() * this.songs.length)
          } while (this.playedIndexes.includes(newIndex))
          this.playedIndexes.push(newIndex)
          if (this.playedIndexes.length === this.songs.length) {
            this.playedIndexes = []
          }
          this.currentIndex = newIndex
          this.loadCurrentSong()
        },
Sửa lại hàm playRandomSong

Mỗi bài sẽ chỉ được random 1 lần!

2. Fix lỗi khi tua bài hát, click giữ một chút sẽ thấy lỗi, vì event updatetime nó liên tục chạy dẫn tới lỗi
Thay sự kiện onchange thành oninput:
progress.oninput = function(e) {
            const seekTime = audio.duration / 100 * e.target.value
            audio.currentTime = seekTime
          };
3. Fix lỗi khi next tới 1-3 bài đầu danh sách thì không “scroll into view”

Thay block: 'nearest' thành block: 'center'
scrollToActiveSong: function () {
          setTimeout(() => {
            $('.song.active').scrollIntoView({
              behavior:'smooth',
              block: 'center'
            })
          }, 300)
        },

4. Lưu lại vị trí bài hát đang nghe, F5 lại ứng dụng không bị quay trở về bài đầu tiên
thêm dòng this.currentIndex = this.config.currentIndex vào hàm loadConfig
loadConfig: function () {
          this.isRandom = this.config.isRandom
          this.isRepeat = this.config.isRepeat
          this.currentIndex = this.config.currentIndex

          //Object.assign(this, this.config)

        },
this.setConfig("currentIndex", this.currentIndex)
thêm dòng này vào hàm loadCurrentSong

5. Thêm chức năng điều chỉnh âm lượng, lưu vị trí âm lượng người dùng đã chọn. Mặc định 100%

<!--Adjust Volume -->
      <div class="container">
        <div class="btn btn-down-volume">
          <i class="fa-solid fa-subtract"></i>
        </div>

        <div class="btn btn-volume">
          <i class="fa-solid fa-volume-high "></i>
        </div>
        <div class="btn btn-up-volume">
          <i class="fa-solid fa-plus"></i>
        </div>

      </div>

      <input type="range" class="volume-slider" min="0" max="1" step="0.1" value="0">
      <!-- <span class="volume-value">50%</span> -->
Thêm các icon
const app = {
        currentIndex: 0,
        isPlaying: false,
        isRandom: false,
        isRepeat: false,
        playedIndexes: [],
        currentVolume: 0,
Thêm biến currentVolume để lưu âm lượng hiện tại
// Xử lý tăng giảm âm lượng
          upVolumeBtn.onclick = function () {
            console.log(audio.volume)
            if (audio.volume < 1.0) {
              
              audio.volume = parseFloat((audio.volume + 0.1).toFixed(1));
              volumeSlider.value = audio.volume;
              console.log(volumeSlider)
              _this.setConfig('currentVolume', volumeSlider.value)
            }
          }

          downVolumeBtn.onclick = function () {
            console.log(audio.volume)
            if (audio.volume > 0.0) {
              audio.volume = parseFloat((audio.volume - 0.1).toFixed(1));
              volumeSlider.value = audio.volume;
              console.log(volumeSlider)
              _this.setConfig('currentVolume', volumeSlider.value)
            }
          }

          // Cập nhật giá trị volume slider khi giá trị volume bị thay đổi
          audio.onvolumechange = function () {
            volumeSlider.value = audio.volume;
          }

thêm 2 dong ở hàm loadConfig
loadConfig: function () {
          this.isRandom = this.config.isRandom
          this.isRepeat = this.config.isRepeat
          this.currentIndex = this.config.currentIndex
          this.playedIndexes.push(this.config.currentIndex)
          volumeSlider.value = this.config.currentVolume
          audio.volume = this.config.currentVolume
          //Object.assign(this, this.config)
        },

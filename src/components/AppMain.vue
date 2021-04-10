<template>
  <main>
    <p class="message">
      <a href="https://goukakuweb3.o-hara.ac.jp/" target="_blank"> 合格Web</a>
      の動画を一括ダウンロードするコマンドの自動作成ツールです。
    </p>
    <label>最初の動画のURL</label>
    <input
      class="url"
      placeholder="https://www.resostream.jp/v/mp4/..."
      type="text"
      v-model="movie.url"
      @input="makeDownloadCommand"
    />
    <label>講義の動画数</label>
    <input class="number" type="number" v-model="movie.count" />
    <label>タイトル</label>
    <input
      class="title"
      placeholder="財務会計論"
      type="text"
      v-model="movie.title"
    />
    <button v-if="false" class="make" @click="makeDownloadCommand">
      コマンド生成･コピー
    </button>
    <span class="copied-message" v-if="showCopiedMessage"
      >コピーされました！</span
    >
    <button v-if="false" class="copy" @click="copyCommand">
      クリップボードにコピー
    </button>
    <div class="commands">
      <p v-for="(command, key) in commands" :key="key">{{ command }}</p>
    </div>
    <div class="command-copy">
      <pre class="command">{{ commands.toString().replace(/,/g, "\n") }}</pre>
    </div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      movie: {
        url: "",
        title: "",
        count: 2,
      },
      dividedUrl: {
        first: "",
        second: "",
      },
      showCopyButton: false,
      showCopiedMessage: false,
      commands: ["ここにコマンドが表示されます"],
    };
  },
  methods: {
    makeDownloadCommand() {
      const divided = this.movie.url.split("01.mp4");
      if (!this.movie.url || !this.movie.title || !this.movie.count) {
        return alert("必須項目が入力されていません。");
      }
      if (divided.length != 2) {
        return alert(
          "URLにエラーがあります。1つ目の動画のURLかどうかを確認してください。"
        );
      }

      const mkdir = `mkdir ${this.movie.title}`;
      const change = `cd ${this.movie.title}`;
      const back = "cd ../";

      this.dividedUrl.first = divided[0];
      this.dividedUrl.second = divided[1];
      this.commands = [];

      this.commands.push(mkdir);
      this.commands.push(change);

      for (let i = 0; i < this.movie.count; i++) {
        const movIndex = this.makeNumTwoDigits(i + 1);
        let command = `curl -s -o ${this.movie.title}_${movIndex}.mp4 ${this.dividedUrl.first}${movIndex}.mp4${this.dividedUrl.second}`;
        let message = `echo "${
          this.movie.title
        }の動画をダウンロードしています…  ${i + 1}/${this.movie.count}"`;
        this.commands.push(message);
        this.commands.push(command);
      }

      const completeMessage = `echo "\n${this.movie.count}個の${this.movie.title}の動画のダウンロードが完了しました。\n"`;

      this.commands.push(completeMessage);
      this.commands.push(back);
      // this.showCopyButton = true;
      setTimeout(() => {
        this.copyCommand();
        setTimeout(() => {
          this.movie.url = "";
          let titleIndex = this.movie.title.slice(-1);
          const newTitle = this.movie.title.slice(0, -1);
          titleIndex++;
          this.movie.title = `${newTitle}${titleIndex.toString()}`;
          console.log(this.movie.title);
          console.log(titleIndex);
        }, 500);
      }, 500);
    },
    makeNumTwoDigits(num) {
      if (num < 10) {
        num = `0${num}`;
      }
      return num;
    },
    copyCommand() {
      const copyText = this.$el.querySelector(".command").textContent;
      navigator.clipboard
        .writeText(copyText)
        .then(() => {
          console.log("テキストコピー完了");
          this.copied();
        })
        .catch((e) => {
          console.error(e);
        });
    },

    copied() {
      this.showCopiedMessage = true;
      setTimeout(() => {
        this.showCopiedMessage = false;
      }, 1000);
    },
  },
};
</script>

<style scoped>
main {
  padding: 32px;
  font-size: 20px;
}

a {
  text-decoration: none;
  color: darkblue;
  transition: 0.3s;
}

a:hover {
  opacity: 0.6;
}

p.message {
  margin-bottom: 30px;
}

label {
  display: block;
  font-size: 20px;
  margin-bottom: 8px;
}

input {
  display: block;
  padding: 4px;
  margin-bottom: 20px;
  width: 100%;
  font-size: 20px;
}

input.number {
  width: 60px;
}

input.title {
  width: 300px;
}

button {
  margin-bottom: 20px;
  color: white;
  appearance: none;
  -webkit-appearance: none;
  border: none;
  padding: 6px 16px;
  font-weight: bold;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
}

button.make {
  background: blue;
  margin-right: 12px;
}
button.copy {
  display: block;
  background: darkred;
  margin-right: 12px;
}
.commands {
  padding: 16px;
  background: lightgray;
  border-radius: 4px;
  font-size: 16px;
  color: #555;
  word-break: break-all;
}

.command-copy {
  width: 0;
  height: 0;
  position: absolute;
  top: 0;
  visibility: hidden;
}

span {
  color: #444;
  font-size: 14px;
}
</style>

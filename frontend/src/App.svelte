<script>
  import Fa from 'svelte-fa'
  import { faSearch } from '@fortawesome/free-solid-svg-icons'
  import logo from './assets/images/logo-universal.png'

  import { WebScrapingRace } from "../wailsjs/go/main/App.js"

  import HorseCard from "./components/HorseCard.svelte"
  import RaceResults from "./components/RaceResults.svelte"

  let url = ""
  let searchResult = {}
  let raceResults = []
  let selectHorse = {
    name: "",
    imgUrl: "",
    pedigree: {}
  }
  let message = ""

  function webScrapingRace() {
      message = "ロード中";
    WebScrapingRace(url).then(result => {
      searchResult = result;
      message = "読み込み完了";
    }).catch(error => {
      message = error;
    })
  }

  function readRaceResults(index) {
    raceResults = searchResult.horses[index].results;
    selectHorse.name = searchResult.horses[index].name;
    selectHorse.imgUrl = searchResult.horses[index].img_url;
    selectHorse.pedigree = searchResult.horses[index].pedigree;
  }
</script>

<div class="container-top container vh-100">
  <div class="row vh-100">
    <div class="col align-self-center">
      <div class="card-rotate card shadow">
        <div class="card-body card-rotate-text">
          <div class="text-center pt-4 mb-3">
            <img
              id="logo"
              alt="Wails logo"
              src="{logo}"
              class="card-img-top h-50 w-50"
            >
          </div>

          <div class="container">
            <div class="text-center">
              <h1 class="display-4">こんにちは!</h1>
              <p class="lead">netkeibaのレースURL情報を打ち込むと、優先的に見たい地方競馬情報が表示されます</p>
            </div>
          </div>

          <div>
            <div class="text-center mb-3">
              {#if message === "ロード中"}
                <div class="spinner-border" role="status">
                  <span class="visually-hidden">Loading...</span>
                </div>
              {/if}
              <p>{message}</p>
            </div>

            <div class="input-group mb-3">
              <input
                type="text"
                class="form-control"
                placeholder="URLを入力"
                bind:value={url}
              >
              <button
                class="btn btn-outline-success"
                type="button"
                on:click={webScrapingRace}
              >
                <Fa icon={faSearch} />
                <i class="fas fa-search"></i> 生成
              </button>
            </div>

            <div class="mb-3">
              {#if Object.keys(searchResult).length !== 0}
                <div class="mb-3">
                  <h4>{searchResult.name}({searchResult.racetrack}{searchResult.type}{searchResult.distance}m)</h4>
                </div>

                <table class="table align-middle">
                  <thead>
                    <tr>
                      <th scope="col">#</th>
                      <th scope="col">名前</th>
                      <th scope="col">レース数</th>
                      <th scope="col">勝利数</th>
                      <th scope="col">敗北数</th>
                      <th scope="col">勝率</th>
                      <th scope="col">コース適正</th>
                      <th scope="col">距離適正</th>
                      <th scope="col">脚質</th>
                      <th scope="col">重馬場</th>
                    </tr>
                  </thead>
                  <tbody>
                    {#each searchResult.horses as horse, i}
                      <tr>
                        <th>{i + 1}</th>
                        <td><a href="#race_results" class="link-primary" on:click={() => readRaceResults(i)}>{horse.name}</a></td>
                        <td>{horse.play_game_count}</td>
                        <td>{horse.win}</td>
                        <td>{horse.lose}</td>
                        <td>{Math.floor(horse.wp)}%</td>
                        <td>{horse.course_aptitude}</td>
                        <td>{horse.distance_aptitude}</td>
                        <td>{horse.running_style}</td>
                        <td>{horse.heavy_racetrack}</td>
                      </tr>
                    {/each}
                  </tbody>
                </table>
              {/if}
            </div>

            <div id="race_results" class="mb-3">
              {#if Object.keys(raceResults).length !== 0}
                <div class="mb-3">
                  <RaceResults
                    raceResults={raceResults}
                    activeDistance={searchResult.distance}
                  />
                </div>

                <div class="mb-3 w-100 d-flex justify-content-center">
                  <HorseCard horse={selectHorse} />
                </div>
              {/if}
            </div>
          </div>

        </div>
      </div>
    </div>
  </div>
</div>

<style>
.card-rotate {
  transform: rotate(2deg);
}
.card-rotate-text {
  transform:skew(0deg, -2deg);
}

.container-top {
  max-width: 960px;
}

</style>

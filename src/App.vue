<template>
  <div id="app">
    <h1>Hiiii!</h1>
    <div class="bucket-wrapper" v-for="(ep, i) in episodes" :key="i">
      <svg class="bucket-svg" viewBox="-200 -200 400 400">
        <g id="bucket" :transform="`scale(${ep.bucketSize})`">
          <path d="M-50,-50 L-40,50 C-20,60,20,60,40,50 L50,-50" stroke="black" fill="none" />
          <ellipse cx="0" cy="-50" rx="50" ry="10" fill="lightblue" stroke="black" />
          <path d="M-45,-40 C-20,10,70,25,49,-40" stroke="black" fill="none" />
          <g id="eyes">
            <circle cx="30" cy="20" r="5" />
            <circle cx="0" cy="20" r="5" />
          </g>
          <path d="M-10,0 Q0,10,10,0" transform="translate(15, 30)" stroke="black" fill="none" />
        </g>
        <g
          id="halo"
          :transform="`translate(${-30 * ep.bucketSize}, ${-80 * ep.bucketSize})scale(${ep.haloSize})`"
        >
          <ellipse
            cx="0"
            cy="0"
            rx="60"
            ry="15"
            fill="none"
            stroke="goldenrod"
            transform="rotate(-15)"
            stroke-width="4"
          />
        </g>
      </svg>
      <div class="label">{{ep.label}}</div>
    </div>
    <!-- <svg width="1300" height="10000">
      <g v-for="(ep, i) in episodes" :key="i" :transform="`translate(${i % 5 * epWidth}, ${Math.floor(i / 5) * epHeight})`">
        <path :transform="`scale(${ep.haloSize})`" class="halo" d="M80.77,13.83C53.9,19.95,34.18,33.99,36.73,45.18c2.55,11.2,26.4,15.32,53.28,9.2
	c26.87-6.12,46.59-20.16,44.04-31.35S107.65,7.71,80.77,13.83z M82.41,45.48c-19.11,3.94-35.74,1.65-37.13-5.11
	S58.25,24.94,77.36,21c19.11-3.94,35.74-1.65,37.13,5.11C115.89,32.87,101.52,41.54,82.41,45.48z"/>
        <g :transform="`scale(${ep.bucketSize})`">
          <ellipse class="bucket-outline" cx="130.71" cy="82.48" rx="66.73" ry="17.57"/>
          <path class="bucket-outline" d="M130.71,100.05c-29.27,0-54.12-4.96-63.12-11.87l-0.02,0l17.82,110.74c0,0,25.38,13.89,48.88,13.89
            c34.01,0,51.28-17.27,51.28-17.27l9.26-108.17l-0.02,0C186.73,94.7,161.1,100.05,130.71,100.05z"/>
          <path class="handle" d="M89.99,117.83c0,0-5.55,51.81,64.2,51.43c69.08-0.38,38.41-49.55,38.41-49.55"/>
          <circle cx="138.62" cy="128.48" r="6.01"/>
          <circle cx="164.9" cy="125.9" r="6.01"/>
        </g>
        <text :x="epWidth / 2" :y="epHeight - 30" text-anchor="middle">{{ep.label}}</text>
      </g>
    </svg>-->
  </div>
</template>

<script>
/* eslint-disable */
import * as d3 from "d3";
import _ from "lodash";
import rawData from "../data/lwj.json";

export default {
  name: "app",
  data() {
    return {
      episodes: [],
      epWidth: 274,
      epHeight: 215
    };
  },
  mounted() {
    // average_view_duration: 10.6373
    // episode_date: "Jan 31, 2019"
    // guest_twitter_following: 808
    // level: "beginner"
    // part_of_stack: "frontend"
    // percent_liked_vs_disliked (%): 100
    // role: "student"
    // shares: 80
    // video: "SLGkyodumKI"
    // video_title: "Build a Portfolio Site with Sanity.io and Gatsby — Learn With Jason"
    // views: 6011
    // watch_time_minutes: 63941.0752
    const viewDurationDomain = d3.extent(
      rawData,
      ({ average_view_duration }) => average_view_duration
    );
    const twitterFollowingDomain = d3.extent(
      rawData,
      ({ guest_twitter_following }) => guest_twitter_following
    );
    const sharesDomain = d3.extent(rawData, ({ shares }) => shares);

    const bucketScale = d3.scaleLinear(viewDurationDomain, [0.25, 1]);
    const wingScale = d3.scaleLinear(twitterFollowingDomain, [0.25, 1]);
    const haloScale = d3.scaleLinear(sharesDomain, [0.25, 1]);

    this.episodes = _.map(rawData, ep => {
      return {
        bucketSize: bucketScale(ep.average_view_duration),
        wingSize: wingScale(ep.guest_twitter_following),
        haloSize: haloScale(ep.shares),
        label: ep.video_title.replace(" — Learn With Jason", "")
      };
    });
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.bucket-wrapper {
  display: inline-block;
  width: 300px;
}

.bucket-svg {
  width: 300px;
  height: 300px;
}

.bucket-color {
  fill: #f2f0f1;
}
.bucket-fill {
  fill: #d6a9b2;
}
.star {
  fill: #eacf5d;
}
.wing1 {
  fill: #b2dfd6;
}
.wing2 {
  fill: #b2dfd6;
  stroke: #ffffff;
  stroke-miterlimit: 10;
}
.halo {
  fill: #f2dd83;
}
.bucket-outline {
  fill: none;
  stroke: #000000;
  stroke-width: 3;
  stroke-miterlimit: 10;
}
.handle {
  fill: none;
  stroke: #000000;
  stroke-width: 3;
  stroke-linecap: round;
  stroke-miterlimit: 10;
}
</style>

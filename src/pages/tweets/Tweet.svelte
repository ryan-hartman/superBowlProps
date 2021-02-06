<script>
    import { each } from "svelte/internal";
    import { Card, Row, Col } from 'svelte-chota';
    import { HeartIcon, TwitterIcon, RepeatIcon } from 'svelte-feather-icons'
    import Carousel from '@beyonk/svelte-carousel'
  
    export let tweet;
  
  </script>
  
  <style>
    .tweetstyle {
      background-color: #243447;
    }
  </style>
  
  <div class="tweetstyle">
    <Card style="max-width:550px;background-color: #243447">
      <h5 slot="header">@{tweet.handle}</h5>
      {tweet.Text}
      <br>
      {#if tweet.ImageURLs.length > 0}
        <div>
          <Carousel perPage={1}>
            {#each tweet.ImageURLs as ImageUrl}
            <div class="slide-content">
              <a href={ImageUrl}>
                <img src={ImageUrl} alt="" target="_blank"/>
              </a>
            </div>
            {/each}
          </Carousel>
        </div>
      {/if}
      <Row>
        <Col>
          <HeartIcon size="1x" strokeWidth="1"/>
          {tweet.FavoriteCount}
        </Col>
  
        <Col>
          <RepeatIcon size="1x" strokeWidth="1"/>
          {tweet.RetweetCount}
        </Col>
      </Row>
      <Row>
        <Col>
          {new Date(tweet.CreatedAt).toDateString()}
        </Col>
        <Col>
          <a href= {tweet.Url}><TwitterIcon size="1x"/></a>
        </Col>
      </Row>
    </Card>
  </div>
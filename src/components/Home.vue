<template>
  <div class="home" v-if="datas && datas.length">
    <div v-for="(data_v, data_i) in datas" :key="data_i">
      <section>
        <div class="container">
          <div class="bs-callout bs-callout-danger bs-callout-mod">
            <h4>{{ data_v.name }}</h4>
          </div>
        </div>
      </section>
      <div class="container">
        <section class="hero container-hero">
          <div class="hero-body">
              <div class="columns">
                <div class="column is-one-quarter" align="center">
                  <figure class="image is-128x128">
                    <img src="http://bulma.io/images/placeholders/128x128.png">
                  </figure>
                </div>
                <div class="column">
                  <table class="table table-hero">
                    <tr>
                      <td>Height</td>
                      <td>:</td>
                      <td>{{ data_v.height }}</td>
                    </tr>
                    <tr>
                      <td>Mass</td>
                      <td>:</td>
                      <td>{{ data_v.mass }}</td>
                    </tr>
                    <tr>
                      <td>Hair Color</td>
                      <td>:</td>
                      <td>{{ data_v.hair_color }}</td>
                    </tr>
                    <tr>
                      <td>Skin Color</td>
                      <td>:</td>
                      <td>{{ data_v.skin_color }}</td>
                    </tr>
                    <tr>
                      <td>Birth Year</td>
                      <td>:</td>
                      <td>{{ data_v.birth_year }}</td>
                    </tr>
                    <tr>
                      <td>Gender</td>
                      <td>:</td>
                      <td>{{ data_v.gender }}</td>
                    </tr>
                  </table>
                </div>
              </div>
          </div>
        </section>
        <section class="margin">
          <div class="columns">
            <div class="column is-two-thirds">
              <nav class="level">
                <div class="level-left">
                  <div class="level-item">
                    <h4 class="title is-4 title-mod">{{ data_v.last_name }}'s Movie</h4>
                  </div>                
                </div>

                <!-- Right side -->
                <div class="level-right">
                  <div class="level-item">
                    <a href="#">See More</a>
                  </div>
                </div>
              </nav>
              <div class="columns is-mobile" v-if="movie_s && movie_s.length">
                <div class="column is-3-desktop is-6-mobile" v-for="(movies_v, movies_i) in movie_s" :key="movies_i">
                  <div class="card">
                    <div class="card-image">
                      <figure class="image is-4by3">
                        <img src="http://bulma.io/images/placeholders/1280x960.png" alt="Placeholder image">
                      </figure>
                    </div>
                    <div class="card-content">
                      <h6 class="title is-6">{{ movies_v.trimmed_title }}</h6>
                      
                      <div class="content">
                        <p class="subtitle is-6">Director:
                          <br>
                          {{ movies_v.director }}
                        </p>
                        <p class="subtitle is-6">Release:
                          <br>
                          {{ movies_v.release_date }}
                        </p>
                      </div>
                    </div>
                  </div>
                </div>
                
              </div>
            </div>
            <div class="column">
              <nav class="level">
                <div class="level-left">
                  <div class="level-item">
                    <h4 class="title is-4 title-mod">Related Another Movies</h4>
                  </div>                
                </div>
              </nav>
              <div class="columns">
                <div class="column is-12">
                  
                  <div class="columns" v-if="relateds && relateds.length">
                    <div class="column" v-for="(related_v, related_i) in relateds" :key="related_i">
                      <div class="column-mod">
                        <table class="table table-related">
                          <tr>
                            <td colspan="2">{{ related_v.title }}</td>
                          </tr>
                          <tr>
                            <td width="40%"><h5 class="related-dtl">Director:</h5></td>
                            <td><h5 class="related-dtl">{{ related_v.director }}</h5></td>
                          </tr>
                          <tr>
                            <td><h5 class="related-dtl">Producer:</h5></td>
                            <td><h5 class="related-dtl">{{ related_v.producer }}</h5></td>
                          </tr>
                          <tr>
                            <td><h5 class="related-dtl">Release Date:</h5></td>
                            <td><h5 class="related-dtl">{{ related_v.release_date }}</h5></td>
                          </tr>
                        </table>
                      </div>
                      <div class="column-mod-2">
                        <div class="column is-12">
                          <p>
                            {{ related_v.opening_crawl }}
                          </p>
                        </div>
                        <div class="column is-12" align="right">
                          <a href="#">See More</a>
                        </div>
                      </div>
                    </div>
                  </div>
                  
                </div>
              </div>
            </div>
          </div>
        </section>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'home',
  data: () => ({
    profiles: [],
    last_name_v: '',
    movies_urls: [],
    movies: [],
    movie_s: [],
    relateds: [],
    errors: [],
    datas:[]
  }),

  created(){
    //get profile
    axios.get('https://swapi.co/api/people/')
      .then(response => {
        this.profiles = response.data.results.slice(0,2);
        for(var i = 0; i < this.profiles.length; i++){
          var profile_val = this.profiles[i];
          //cut last name
          var full_name_o           = profile_val.name;
          var full_name_s           = full_name_o.split(' ');
          var last_name             = full_name_s[full_name_s.length-1];
          profile_val['last_name']  = last_name;
          
          var movie_urls            = profile_val.films.splice(0,4);
          profile_val['movie_details'] = [];
          for (var j = 0; j < movie_urls.length; j++) {
            axios.get(movie_urls[j])
              .then(response => {
                profile_val['movie_details'].push(response.data);
              })
              .catch(e => {
                this.errors.push(e);
              });
          }
          this.datas.push(profile_val);
        }
      })
      .catch(e => {
        this.errors.push(e);
      });
  } 
}

/**
for(var profile_val of this.profiles){
          //cut last name
          var full_name_o   = profile_val.name;
          var full_name_s   = full_name_o.split(' ');
          var last_name     = full_name_s[full_name_s.length-1];
          this.last_name_v  = last_name;

          this.movies_urls  = profile_val.films.slice(0,4);
          var related_url   = profile_val.films.slice(4,5);
          //get list of movies
          for(var movie_url_val of this.movies_urls){
            axios.get(movie_url_val)
              .then(response => {
                this.movies.push(response.data);
                //get title name
                for(var mov_value of this.movies){
                  var length        = 20;
                  var mov_title     = mov_value.title;
                  //trim title name by 20 characters
                  var trimmed_title = mov_title.length > length ? mov_title.substring(0, length - 3) + "..." : mov_title;
                  response.data['trimmed_title'] = trimmed_title;
                }
                this.movie_s.push(response.data);
              })
              .catch(e => {
                this.errors.push(e);
              });
          }
          //get related
          for(var related_url_val of related_url){
            axios.get(related_url_val)
              .then(response => {
                this.relateds.push(response.data);
              })
              .catch(e => {
                this.errors.push(e);
              });
          }
        }
 */
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="sass" scoped>
@import '../mq'
.bs-callout 
    padding: 20px
    margin: 20px 0
    border: 1px solid #eee
    border-left-width: 5px
    border-radius: 3px
    h4 
      margin-top: 0
      margin-bottom: 5px
    p:last-child 
      margin-bottom: 0
    code 
      border-radius: 3px

.bs-callout+.bs-callout 
    margin-top: -5px

.bs-callout-danger 
    border-left-color: $primary
    h4 
      color: $primary

.bs-callout-mod 
    padding: 0
    padding-left: 20px
    border-top: none
    border-right: none
    border-bottom: none
    border-radius: 0px
    h4 
      font-family: 'Lato', 'Roboto'
      color: #333
      font-weight: bolder

.container-hero
  background-color: $black

.table-hero, .table-hero tr:hover, .table-related, .table-related tr:hover
  background: $black
  color: white
.table-related
  margin: 0
  tr:nth-child(n+2) td
    color: white
    padding-top: 0px
    padding-bottom: 0px
  tr:last-child td
    padding-bottom: 10px

.table-hero td, .table-related td
  border: none
  text-align: left
  font-family: 'Lato', 'Roboto'
  font-weight: bold
.table-hero td
  padding-top: 2px
  padding-bottom: 2px

.margin
  margin-top: 10px

.title-mod
  color: $red

.column-mod
  background: $black
.column-mod-2
  background: white
  box-shadow: 0px 2px 3px rgba(10, 10, 10, 0.1)

.related-dtl
  text-align: left
  font-family: 'Lato', 'Roboto'


.title, .subtitle
  font-family: 'Lato', 'Roboto'

.columns  
  flex-wrap: wrap

.card-content
  padding: 10px
</style>

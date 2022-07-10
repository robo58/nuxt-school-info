<template>
  <v-row justify="center" align="center">
    <v-col cols="12">
      <v-card>
        <v-card-title class="headline">
          School information system
        </v-card-title>
        <v-card-text class="mt-6">
          <v-row>
            <v-col>
              <v-btn @click="oauthLogin">OAuth Login</v-btn>
              <v-btn @click="oidcLogin">OpenID Login</v-btn>
            </v-col>
            <v-col>
              <v-row>Access token: {{ accessToken }}</v-row>
              <v-row>ID token: {{ idToken }}</v-row>
              <v-row>Scopes: {{ scopes }}</v-row>
              <v-row>Name: {{ getName }}</v-row>
              <v-row>Email: {{getEmail}}</v-row>
              <v-row>Roles: {{ getRoles }}</v-row>
            </v-col>
          </v-row>
          <v-row class="d-flex justify-space-between align-center mt-5">
            <v-btn @click="getInfo">Get available info</v-btn>
            <v-btn @click="getUser">Get user info</v-btn>
          </v-row>
          <v-row>
            <v-expansion-panels dark>
              <h2>Schools</h2>
              <v-expansion-panel v-for="(school,i) in getSchools" :key="i">
                <v-expansion-panel-header>
                    <h3>{{ school.name }}</h3>
                </v-expansion-panel-header>
                <v-expansion-panel-content>
                  <v-card>
                    <v-card-text>
                      <v-row>
                        <v-col>
                          <v-row>Name: {{ school.name }}</v-row>
                          <v-row>Headmaster name: {{school.headmaster.name}}</v-row>
                        </v-col>
                      </v-row>
                      <v-row class="ma-5 pa-5" v-if="school.departments">
                        <h2>Departments</h2>
                        <v-expansion-panels>
                          <v-expansion-panel v-for="(department,i) in school.departments" :key="department.ID">
                            <v-expansion-panel-header>
                                <h3>{{ department.name }}</h3>
                            </v-expansion-panel-header>
                            <v-expansion-panel-content>
                              <v-card>
                                <v-card-text>
                                  <v-row>
                                    <v-col>
                                      <v-row>Name: {{ department.name }}</v-row>
                                      <v-row>Headroom teacher name: {{department.headroom_teacher.name}}</v-row>
                                    </v-col>
                                  </v-row>
                                  <v-row class="ma-5 pa-5" v-if="department.subjects">
                                    <h2>Department subjects</h2>
                                    <v-expansion-panels>
                                      <v-expansion-panel v-for="(subject,i) in department.subjects" :key="subject.ID">
                                        <v-expansion-panel-header>
                                            <h3>{{ subject.name }}</h3>
                                        </v-expansion-panel-header>
                                        <v-expansion-panel-content>
                                          <v-card>
                                            <v-card-text>
                                              <v-row>
                                                <v-col>
                                                  <v-row>Name: {{ subject.name }}</v-row>
                                                  <v-row>Teacher name: {{subject.teacher.name}}</v-row>
                                                </v-col>
                                              </v-row>
                                            </v-card-text>
                                          </v-card>
                                        </v-expansion-panel-content>
                                      </v-expansion-panel>
                                    </v-expansion-panels>
                                  </v-row>
                                  <v-row class="ma-5 pa-5" v-if="department.students">
                                    <h2>Department students</h2>
                                    <v-expansion-panels>
                                      <v-expansion-panel v-for="(student,i) in department.students" :key="student.ID">
                                        <v-expansion-panel-header>
                                            <h3>{{ student.name }}</h3>
                                        </v-expansion-panel-header>
                                        <v-expansion-panel-content>
                                          <v-card>
                                            <v-card-text>
                                              <v-row>
                                                <v-col>
                                                  <v-row>Name: {{ student.name }}</v-row>
                                                  <v-row>Email: {{student.email}}</v-row>
                                                </v-col>
                                              </v-row>
                                              <v-row class="ma-5 pa-5" v-if="student.subjects">
                                                <h2>Student subjects</h2>
                                                <v-expansion-panels>
                                                  <v-expansion-panel v-for="(subject,i) in student.subjects" :key="subject.ID">
                                                    <v-expansion-panel-header>
                                                        <h3>{{ subject.name }}</h3>
                                                    </v-expansion-panel-header>
                                                    <v-expansion-panel-content>
                                                      <v-card>
                                                        <v-card-text>
                                                          <v-row>
                                                            <v-col>
                                                              <v-row>Name: {{ subject.name }}</v-row>
                                                              <v-row>Grade: {{ subject.grade }}</v-row>
                                                              <v-row>Teacher name: {{subject.teacher.name}}</v-row>
                                                            </v-col>
                                                          </v-row>
                                                        </v-card-text>
                                                      </v-card>
                                                    </v-expansion-panel-content>
                                                  </v-expansion-panel>
                                                </v-expansion-panels>
                                              </v-row>
                                            </v-card-text>
                                          </v-card>
                                        </v-expansion-panel-content>
                                      </v-expansion-panel>
                                    </v-expansion-panels>
                                  </v-row>
                                </v-card-text>
                              </v-card>
                            </v-expansion-panel-content>
                          </v-expansion-panel>
                        </v-expansion-panels>
                      </v-row>
                    </v-card-text>
                  </v-card>
                </v-expansion-panel-content>
              </v-expansion-panel>
            </v-expansion-panels>
          </v-row>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: 'IndexPage',
  created() {

  },
  methods: {
    async getInfo() {
      try{
        this.info = await this.$axios.$get('/api/schools')
      } catch (e) {
        console.log(e)
      }

    },
    async getUser() {
      try{
        let data = await this.$axios.$get('/oauth/user')
        this.accessToken = data.token;
        this.scopes = data.scopes;
        this.user = data.user;
      } catch (e) {
        console.log(e)
      }

    },
    async oauthLogin() {
      window.open('http://localhost:3000/oauth/client/redirect?redirect_to=' + window.location.origin)
    },
    async oidcLogin(){
      window.open('http://localhost:3000/openid/client/redirect?redirect_to=' + window.location.origin)
    }
  },
  data(){
    return {
      accessToken: null,
      idToken: null,
      scopes: null,
      user: null,
      info: null
    }
  },
    computed:{
      getName(){
        return this.user?.name
      },
      getEmail(){
        return this.user?.email
      },
      getRoles(){
        return this.user?.roles?.map(r => r.name).join(', ')
      },
      getSchools(){
        return this.info?.schools ?? []
      },
    }
}
</script>

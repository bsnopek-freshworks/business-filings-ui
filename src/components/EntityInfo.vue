<template>
  <div id="entity-info" :class="{ 'staff': isRoleStaff }">
    <v-container>

      <!-- Business Name, Business Status -->
      <div class="title-container">
        <div class="mb-1" id="entity-legal-name">
          <span>{{ entityName || 'Not Available' }}</span>
        </div>
        <v-chip id="entity-status" small label text-color="white"
          v-if="entityStatus"
          :class="{
            'blue' : isGoodStanding,
            'red' : isPendingDissolution || isNotInCompliance
          }">
          <span v-if="isGoodStanding">In Good Standing</span>
          <span v-else-if="isPendingDissolution">Pending Dissolution</span>
          <span v-else-if="isNotInCompliance">Not in Compliance</span>
        </v-chip>
      </div>

      <!-- Business Numbers, Contact Info -->
      <div class="business-info">
        <div class="business-info__meta">
          <dl>
            <dt>Business No:</dt>
            <dd class="ml-2" id="entity-business-number">
              <span>{{ entityBusinessNo || 'Not Available' }}</span>
            </dd>
            <dt>Incorporation No:</dt>
            <dd class="ml-2" id="entity-incorporation-number">
              <span>{{ entityIncNo || 'Not Available' }}</span>
            </dd>
          </dl>
        </div>

        <div class="business-info__contact">
          <dl>
            <dt></dt>
            <dd id="entity-business-email" aria-label="Business Email">
              <span>{{businessEmail || 'Unknown Email'}}</span>
            </dd>
            <template v-if="fullPhoneNumber">
              <dt></dt>
              <dd id="entity-business-phone" aria-label="Business Phone">
                <span>{{fullPhoneNumber}}</span>
              </dd>
            </template>
          </dl>
          <v-menu bottom left offset-y content-class="v-menu">
            <template v-slot:activator="{ on }">
              <v-btn id="entity-settings-button" small icon color="primary" v-on="on">
                <v-icon small>mdi-settings</v-icon>
              </v-btn>
            </template>
            <v-list class="pt-0 pb-0">
              <v-list-item id="update-business-profile-menuitem" @click="editBusinessProfile()">
                <v-list-item-title>Update business profile</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-menu>
        </div>
      </div>

    </v-container>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import { mapState, mapGetters } from 'vuex'
import { EntityStatus } from '@/enums'

@Component({
  computed: {
    // Property definitions for runtime environment.
    ...mapState(['entityName', 'entityStatus', 'entityBusinessNo', 'entityIncNo',
      'businessEmail', 'businessPhone', 'businessPhoneExtension']),
    ...mapGetters(['isRoleStaff'])
  }
})
export default class EntityInfo extends Vue {
  // Local definitions of computed properties for static type checking.
  // Use non-null assertion operator to allow use before assignment.
  readonly entityName!: string
  readonly entityStatus!: string
  readonly entityBusinessNo!: string
  readonly entityIncNo!: number
  readonly businessEmail!: string
  readonly businessPhone!: string
  readonly businessPhoneExtension!: string
  readonly isRoleStaff!: boolean

  /**
   * Computed value.
   * @returns The business phone number and optional extension, or null.
   */
  private get fullPhoneNumber (): string {
    if (!this.businessPhone) return null
    return `${this.businessPhone}${this.businessPhoneExtension ? (' x' + this.businessPhoneExtension) : ''}`
  }

  /**
   * Computed values.
   * @returns True if the entity has the subject status.
   */
  private get isGoodStanding (): boolean {
    return this.entityStatus === EntityStatus.GOODSTANDING
  }

  private get isPendingDissolution (): boolean {
    return this.entityStatus === EntityStatus.PENDINGDISSOLUTION
  }

  private get isNotInCompliance (): boolean {
    return this.entityStatus === EntityStatus.NOTINCOMPLIANCE
  }

  /**
   * Redirects the user to the Auth UI to update their business profile.
   */
  private editBusinessProfile (): void {
    const authUrl = sessionStorage.getItem('AUTH_URL')
    const businessProfileUrl = authUrl + 'businessprofile'

    // assume Business Profile URL is always reachable
    window.location.assign(businessProfileUrl)
  }
}
</script>

<!-- eslint-disable max-len -->
<style lang="scss" scoped>
// TODO: Explore how to expose this globally without having to include in each module
// see https://cli.vuejs.org/guide/css.html#automatic-imports
// see https://vue-loader-v14.vuejs.org/en/features/postcss.html
@import '@/assets/styles/theme.scss';

#entity-info {
  background: $BCgovInputBG;
}

// #entity-info.staff {
//   background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='105' height='100'><text x='0' y='105' font-size='30' transform='rotate(-45 10,40)' opacity='0.1'>STAFF</text></svg>");
//   background-repeat: repeat-x;
// }

.container {
  padding-top: 1.5rem;
  padding-bottom: 1.5rem;
}

#entity-legal-name {
  display: inline-block;
  color: $gray9;
  letter-spacing: -0.01rem;
  font-size: 1.125rem;
  font-weight: 700;
}

#entity-status {
  margin-top: 0.25rem;
  margin-left: 0.5rem;
  vertical-align: top;
}

.business-info {
  display: flex;
  direction: row;
  justify-content: space-between;
}

dl {
  display: inline-block;
  overflow: hidden;
  color: $gray6;
}

dd, dt {
  float: left;
}

dt {
  position: relative;
}

dd + dt:before {
  content: "•";
    display: inline-block;
    margin-right: 0.75rem;
    margin-left: 0.75rem;
}

.business-info__contact {
  display: flex;
  align-items: center;
}

#entity-settings-button {
  margin-top: 0.1rem;
  margin-left: 0.25rem;
}

#update-business-profile-menuitem {
  min-width: 220px;
}
</style>

<template>
  <v-card flat>
    <div class="certify-container">
      <div class="certifiedby-container">
        <label>Legal Name</label>
        <div class="value certifiedby">
          <v-text-field
            filled
            persistent-hint
            id="certified-by-textfield"
            label="Person's legal name"
            hint="Legal name of authorized person"
            :value="certifiedBy"
            :rules="[ v => !!v || 'A person\'s legal name is required.']"
            @input="emitCertifiedBy($event)"
          />
        </div>
      </div>
      <v-checkbox
        :value="isCertified"
        @change="emitIsCertified($event)"
      >
        <template slot="label">
          <div class="certify-stmt">
            I, <strong>{{trimmedCertifiedBy || '[Legal Name]'}}</strong>, certify that I have relevant knowledge
            of the {{entityDisplay || 'association'}} and that I am authorized to make this filing.
          </div>
        </template>
      </v-checkbox>
      <p class="certify-clause">Date: {{getCurrentDate}}</p>
      <p class="certify-clause">{{message}}</p>
    </div>
  </v-card>
</template>

<script lang="ts">
import { Component, Vue, Prop, Emit } from 'vue-property-decorator'
import { Getter } from 'vuex-class'

@Component({})
export default class Certify extends Vue {
  @Getter getCurrentDate!: string

  /** Certified By prop. */
  @Prop({ default: '' })
  readonly certifiedBy: string

  /** Is Certified prop. */
  @Prop({ default: false })
  readonly isCertified: boolean

  /** Message prop. */
  @Prop({ default: '' })
  readonly message: string

  /** Entity Display prop. */
  @Prop({ default: '' })
  readonly entityDisplay: string

  /** Called when component is created. */
  created (): void {
    // inform parent of initial validity
    this.emitValid(!!this.trimmedCertifiedBy && this.isCertified)
  }

  /** The trimmed "Certified By" string (may be ''). */
  private get trimmedCertifiedBy (): string {
    // remove repeated inline whitespace, and leading/trailing whitespace
    return this.certifiedBy && this.certifiedBy.replace(/\s+/g, ' ').trim()
  }

  /** Emits an event to update the Certified By prop. */
  @Emit('update:certifiedBy')
  private emitCertifiedBy (certifiedBy: string): string {
    // remove repeated inline whitespace, and leading/trailing whitespace
    certifiedBy = certifiedBy && certifiedBy.replace(/\s+/g, ' ').trim()
    this.emitValid(!!certifiedBy && this.isCertified)
    return certifiedBy
  }

  /** Emits an event to update the Is Certified prop. */
  @Emit('update:isCertified')
  private emitIsCertified (isCertified: boolean): boolean {
    this.emitValid(!!this.trimmedCertifiedBy && isCertified)
    return isCertified
  }

  /** Emits an event indicating whether or not the form is valid. */
  @Emit('valid')
  private emitValid (valid: boolean): void { }
}
</script>

<style lang="scss" scoped>
@import '@/assets/styles/theme.scss';

.v-card {
  margin-top: 1rem;
  padding-bottom: 0.5rem;
  padding-top: 1rem;
  line-height: 1.2rem;
  font-size: $px-14;
}

.certify-container {
  padding: 1.25rem;
}

.certifiedby-container {
  display: flex;
  flex-flow: column nowrap;
  position: relative;

  > label:first-child {
    font-weight: 700;
    margin-bottom: 2rem;
  }
}

@media (min-width: 768px) {
  .certifiedby-container {
    flex-flow: row nowrap;

    > label:first-child {
      flex: 0 0 auto;
      padding-right: 2rem;
      width: 12rem;
    }
  }
}

.value.certifiedby {
  width: 100%;
}

.certify-clause {
  padding-left: 2rem;
  color: black;
  font-size: $px-14;
}

.certify-stmt {
  display: inline;
  font-size: $px-14;
  color: black;
}
</style>

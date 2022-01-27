<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="SNOMED International Logo"
          class="shrink mr-2"
          contain
          src="https://www.snomed.org/SNOMED/media/SNOMED/other/brand-mark.png"
          transition="scale-transition"
          width="200"
          height="60"
        />
        <h1>SNOMED CT COVID-19 Coding Guide</h1>
      </div>
      <v-spacer></v-spacer>
      <v-btn color="purple lighten-1">
          <vue-excel-xlsx
            :data="bindings"
            :columns="columns"
            :file-type="'xlsx'"
            :file-name="'ecl-export'"
            :sheet-name="'sheet1'"
            >
            <v-icon right dark class="mr-4">mdi-cloud-download</v-icon>EXPORT BINDINGS
          </vue-excel-xlsx>
      </v-btn>
      <v-menu offset-y>
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            color="purple lighten-1"
            dark
            v-bind="attrs"
            v-on="on"
            class="ml-4"
          >
            <v-icon dark>
              mdi-information
            </v-icon>
          </v-btn>
        </template>
        <v-list>
          <v-list-item-group>
            <v-list-item>
              <v-list-item-icon>
                <v-icon>mdi-github</v-icon>
              </v-list-item-icon>
              <v-list-item-title @click="goToGithub()">
                Source code on GitHub
              </v-list-item-title>
            </v-list-item>
          </v-list-item-group>
          <v-subheader>Bound to SNOMED CT Release:</v-subheader>
          <v-list-item>
            <v-list-item-title>
              Edition
            </v-list-item-title>
            <v-list-item-subtitle>
              International Edition
            </v-list-item-subtitle>
          </v-list-item>
          <v-list-item>
            <v-list-item-title>
              Version
            </v-list-item-title>
            <v-list-item-subtitle>
              20210731
            </v-list-item-subtitle>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>

    <v-main>
      <MainTabs :sections="sections"/>
    </v-main>
  </v-app>
</template>

<script>
import MainTabs from './components/MainTabs.vue';

import VueExcelXlsx from "vue-excel-xlsx";
import Vue from "vue";

Vue.use(VueExcelXlsx);

export default {
  name: 'App',

  components: {
    MainTabs,
  },
  mounted() {
    this.bindings = [];
    for (const section in this.sections) {
      for (const binding in this.sections[section].bindings) {
        this.bindings.push({ section: this.sections[section].title, title: this.sections[section].bindings[binding].title, ecl: this.sections[section].bindings[binding].ecl.replace(/\s\s+/g, ' ') })
      }
    }
    Vue.prototype.$snowstormBase = 'https://snowstorm.ihtsdotools.org/snowstorm/snomed-ct';
    Vue.prototype.$snowstormBranch = 'MAIN';
  },
  methods: {
    goToGithub() {
      window.open('https://github.com/alopezo/coding-demonstrator', '_blank');
    }
  },
  data: () => ({
    bindings: [],
    columns : [
                    {
                        label: "Section",
                        field: "section",
                    },
                    {
                        label: "Title",
                        field: "title",
                    },
                    {
                        label: "ECL",
                        field: "ecl",
                    }
                ],
    sections: {
        'CV19-PFD': {
          title: 'Provider and Facility Details',
          note: 'Information about the healthcare providers and facilities involved with the investigation, treatment, and care of patients with suspected or confirmed COVID-19 are usually recorded in electronic health records. The monitoring of medical equipment availability at healthcare facilities, such as Personal Protective Equipment (PPE) and respiratory devices, is also of utmost importance during the COVID-19 pandemic',
          bindings: {
            'CV19-PFD-HealthcareProfession' : {
              title: 'Healthcare Professional',
              type: 'autocomplete',
              ecl: '<  223366009 |Healthcare professional (occupation)|',
              value: '',
              note: 'The healthcare profession is the occupation or qualification of the healthcare provider who is involved in the investigation, treatment, care or education of the patient - for example  224547003 |Intensive therapy nurse (occupation)| or 76899008 |Infectious disease specialist (occupation)| .'
            },
            'CV19-PFD-SiteOfCare' : {
              title: 'Site of Care',
              type: 'autocomplete',
              ecl: `<  43741000 |Site of care (environment)| 
              OR <<  224884006 |Location within hospital premises (environment)| 
              OR <<   441480003 |Primary care department (environment)| 
              OR <<  440654001 |Inpatient environment (environment)| 
              OR <<  440655000 |Outpatient environment (environment)| 
              OR <<  272497004 |Residential environment (environment)|`,
              value: '',
              note: 'The site of care refers to the location of the specific investigation, treatment or care setting - for example  22232009 |Hospital (environment)| , 309904001 |Intensive care unit (environment)| , or 702917005 |Respiratory disease clinic (environment)| .'
            },
            'CV19-PFD-HealthManagementFinding' : {
              title: 'Health Management Finding',
              type: 'autocomplete',
              ecl: '<  129843006 |Health management finding (finding)|',
              value: '',
              note: 'Health management finding includes concepts that describe the circumstances which affect whether the planned patient outcomes are attained, that different areas within a health organization are running appropriately, that tasks are correctly defined and assessed and that resources are used efficiently. -for example 305456005 |Under care of own general practitioner (finding)| ,  706877002 |Problem with high complexity of treatment regime (finding)| or 129837009 |Ineffective protection (finding)| .'
            },
            'CV19-PFD-PersonalProtectiveEquipment' : {
              title: 'Personal Protective Equipment',
              type: 'autocomplete',
              ecl: `<<  409526008 |Personal protective equipment (physical object)|
              OR <<  6919005 |Protective clothing material, device (physical object)|
              OR <<  706724001 |Face shield (physical object)|
              OR <<  261382003 |Mask (physical object)|
              OR <<  52291003 |Glove, device (physical object)|
              OR <<  15922004 |Gown, device (physical object)|
              OR <<  706154004 |Shoe cover (physical object)|
              OR <<  465982004 |Surgical hood (physical object)|`,
              value: '',
              note: 'Personal protective equipment is protective clothing, or other garments or equipment designed to protect the wearer\'s body from injury or infection - for example  409528009 |Surgical face mask (physical object)| and 255716002 |Latex rubber gloves (physical object)|'
            }
          }
        },
        'CV19-PAT': {
          title: 'Patient Demographics',
          note: 'General demographic characteristics are important to record for each patient being investigated or treated for COVID-19.',
          bindings: {
            'CV19-PAT-BiologicalSex' : {
              title: 'Biological Sex',
              type: 'dropdown',
              ecl: `<  429019009 |Finding related to biological sex (finding)|  
                    MINUS <<  302081005 |Finding of sex of baby (finding)|`,
              value: '',
              note: 'Multiple types of sex and/or gender are recorded in clinical practice, including biological sex and gender identity.'
            },
            'CV19-PAT-GenderIdentity' : {
              title: 'Gender Identity',
              type: 'dropdown',
              ecl: '<  365873007 |Gender finding (finding)|',
              value: '',
              isMultiple: false
            },
            'CV19-PAT-MaritalOrPartnershipStatus' : {
              title: 'Marital or Partnership Status',
              type: 'autocomplete',
              ecl: '<  365581002 |Finding of marital or partnership status (finding)|',
              value: '',
              note: 'The marital or partnership status of a patient describes their legal, civil or personal relationship with their significant other, for example 87915002 |Married (finding)|.'
            },
            'CV19-PAT-Nationality' : {
              title: 'Nationality',
              type: 'autocomplete',
              ecl: '<  223369002 |Country (geographic location)|',
              value: '',
              note: 'Nationality refers to a particular legal relationship between an individual person and a sovereign state. The nationality of a patient may refer to the nation in which they were born, have citizenship, or have other legal ties - for example 223610001 |Jordan (geographic location)| or 223625001 |New Zealand (geographic location)|.'
            },
            'CV19-PAT-EthnicGroup' : {
              title: 'Ethnic Group',
              type: 'autocomplete',
              ecl: '<  372148003 |Ethnic group (ethnic group)|',
              value: '',
              note: 'The ethnic group (or ethnicity) of a patient is the social or cultural group with whom they identify - for example, 33897005 |Chinese (ethnic group)|  or  735001008 |Scandinavian (ethnic group)| .'
            },
            'CV19-PAT-RacialGroup' : {
              title: 'Racial Group',
              type: 'autocomplete',
              ecl: '<  415229000 |Racial group (racial group)|',
              value: '',
              note: 'The racial group (or race) of a patient is the group (or groups) with whom they share inherited physical characteristics - for example,  414408004 |Hispanic (racial group)|  or  413773004 |Caucasian (racial group)| .'
            },
            'CV19-PAT-Occupation' : {
              title: 'Occupation',
              type: 'autocomplete',
              ecl: '<  14679004 |Occupation (occupation)|',
              value: '',
              note: 'Occupation is the patient\'s job or profession - for example,  158942005 |Residential child care worker (occupation)| or  308223007 |Hairdresser (occupation)|.'
            },
            'CV19-PAT-TravelHistory' : {
              title: 'Travel History',
              type: 'autocomplete',
              ecl: `<  223496003 |Geographical and/or political region of the world (geographic location)|
                    OR <  365457007 |Foreign travel history finding (finding)|
                    OR <  420008001 |Travel (event)|`,
              value: '',
              note: 'The travel history of a patient is a record of their past visits to locations both inside and outside their country of residence. This may include a list of the specific countries and/or regions that they have travelled to (e.g.  223498002 |Africa (geographic location)| or  223585008 |South east Asian country (geographic location)| ), or a descriptive travel history finding (e.g.  161090005 |Travel abroad for business (finding)|).'
            },
            'CV19-PAT-ResidentialLocationType' : {
              title: 'Residential Location Type',
              type: 'autocomplete',
              ecl: '<  272497004 |Residential environment (environment)|',
              value: '',
              note: 'The residential location type is the type of place in which the patient is currently living, or was living prior to admission to hospital - for example, 257564005 |Apartment (environment)| or  257670006 |Private house (environment)|.'
            },
            'CV19-PAT-NextOfKinRelationship' : {
              title: 'Next of Kin Relationship',
              type: 'autocomplete',
              ecl: '<  444148008 |Person in family of subject (person)|',
              value: '',
              note: 'The next of kin relationship is the relationship that the patient has with their closest living relative (as chosen by the patient) - for example 444053001 |Husband of subject (person)| or 444301002 |Mother of subject (person)|.'
            },
            'CV19-PAT-LivingArrangements' : {
              title: 'Living Arrangements',
              type: 'autocomplete',
              ecl: '<  365481000 |Finding of household composition (finding)|',
              value: '',
              note: 'The living arrangements of the patient describes the familial and non-familial relationships of a person to all the other people with whom they usually reside - for example  105529008 |Lives alone (finding)| or  224133007 |Lives with family (finding)|.'
            },
            'CV19-PAT-CareAndSupportCircumstances' : {
              title: 'Care and Support Circumstances',
              type: 'autocomplete',
              ecl: '<  365483002 |Finding related to care and support circumstances and networks (finding)|',
              value: '',
              note: 'The care and support circumstances of the patient describe how and by whom they are looked after when living at their usual place of residence - for example  427454004 |Cared for by neighbors (finding)| or 301887005 |Needs assistance at home (finding)|.'
            }
          }
        },
        'CV19-ASS': {
          title: 'Clinical Assessment',
          note: 'The first step in delivering comprehensive care is to undertake a clinical assessment. Please note that all published concepts referred to on this page are included in SNOMED International\'s Global Patient Set (GPS), which is available to be used internationally under the Creative Commons Attribution 4.0 International License. Please refer to http://snomed.org/covid-19 for more information.',
          bindings: {
            'CV19-ASS-Symptoms' : {
              title: 'Symptoms',
              type: 'dropdown',
              ecl: `21522001 |Abdominal pain (finding)| OR 84387000 |Asymptomatic (finding)| OR 131148009 |Bleeding (finding)| OR 29857009 |Chest pain (finding)| OR 43724002 |Chill (finding)| OR 82272006 |Common cold (disorder)| OR 9826008 |Conjunctivitis (disorder)| OR 49727002 |Cough (finding)| OR 3415004 |Cyanosis (finding)| OR 62315008 |Diarrhea (finding)| OR 3006004 |Disturbance of consciousness (finding)| OR 267036007 |Dyspnea (finding)| OR 271807003 |Eruption of skin (disorder)| OR 84229001 |Fatigue (finding)| OR 103001002 |Feeling feverish (finding)| OR 386661006 |Fever (finding)| OR 25064002 |Headache (finding)| OR 66857006 |Hemoptysis (finding)| OR 248567008 |Indrawing of ribs during respiration (finding)| OR 57676002 |Joint pain (finding)| OR 79890006 |Loss of appetite (finding)| OR 44169009 |Loss of sense of smell (finding)| OR 36955009 |Loss of taste (finding)| OR 30746006 |Lymphadenopathy (disorder)| OR 367391008 |Malaise (finding)| OR 68962001 |Muscle pain (finding)| OR 26544005 |Muscle weakness (finding)| OR 68235000 |Nasal congestion (finding)| OR 64531003 |Nasal discharge (finding)| OR 422587007 |Nausea (finding)| OR 44077006 |Numbness (finding)| OR 162397003 |Pain in throat (finding)| OR 38880002 |Rigor (finding)| OR 91175000 |Seizure (finding)| OR 46742003 |Skin ulcer (disorder)| OR 23924001 |Tight chest (finding)| OR 282145008 |Unable to walk (finding)| OR 422400008 |Vomiting (disorder)| OR 56018004 |Wheezing (finding)|`,
              value: '',
              isMultiple: true,
              note: 'The first step in delivering comprehensive care is to undertake a clinical assessment. Please note that all published concepts referred to on this page are included in SNOMED International\'s Global Patient Set (GPS), which is available to be used internationally under the Creative Commons Attribution 4.0 International License. Please refer to http://snomed.org/covid-19 for more information.'
            },
            'CV19-ASS-SymptomsSubtypes' : {
              title: 'Symptoms with subtypes',
              type: 'autocomplete',
              ecl: `<< 21522001 OR << 84387000 OR << 131148009 OR << 29857009 OR << 43724002 OR << 9826008 OR << 49727002 OR << 62315008 OR << 3006004 OR << 267036007 OR << 271807003 OR << 84229001 OR << 103001002 OR << 386661006 OR << 25064002 OR << 66857006 OR << 248567008 OR << 57676002 OR << 79890006 OR << 44169009 OR << 36955009 OR << 30746006 OR << 367391008 OR << 68962001 OR << 68235000 OR << 64531003 OR << 422587007 OR << 162397003 OR << 38880002 OR << 91175000 OR << 46742003 OR << 282145008 OR << 422400008 OR << 56018004 OR << 3415004 OR << 82272006 OR <<• 23924001 OR << 44077006 OR << 26544005`,
              value: '',
              // isMultiple: true,
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected Symptoms codes.'
            },
            'CV19-ASS-Severity' : {
              title: 'Severity',
              type: 'dropdown',
              ecl: `<< 272141005 |Severities (qualifier value)|`,
              value: '',
              note: 'Severity represents the quality of the condition - for example,  24484000 |Severe (severity modifier) (qualifier value)|. Severity may be recorded for each symptom, or as a general statement of their disease state.'
            },
            'CV19-ASS-ClinicalMeasurements' : {
              title: 'Clinical Measurements',
              type: 'dropdown',
              ecl: `413384008 |Ability to perform cognitive activity (observable entity)| OR 442476006 |Arterial oxygen saturation (observable entity)| OR 386725007 |Body temperature (observable entity)| OR 75367002 |Blood pressure (observable entity)| OR 248592006 |Character of cough (observable entity)| OR 364533002 |Color of skin (observable entity)| OR 709017009 |Compliance behavior to safety precaution (observable entity)| OR 263731006 |Coughing (observable entity)| OR 271626009 |Depth of respiration (observable entity)| OR 226379006 |Food intake (observable entity)| OR 364075005 |Heart rate (observable entity)| OR 103228002 |Hemoglobin saturation with oxygen (observable entity)| OR 6942003 |Level of consciousness (observable entity)| OR 440398005 |Level of fatigue (observable entity)| OR 405161002 |Pain level (observable entity)| OR 248750004 |Peripheral blood flow (observable entity)| OR 431314004 |Peripheral oxygen saturation (observable entity)| OR 8499008 |Pulse, function (observable entity)| OR 248565000 |Respiratory effort (observable entity)| OR 86290005 |Respiratory rate (observable entity)| OR 248582003 |Rhythm of respiration (observable entity)| OR 247433003 |Skin elasticity (observable entity)| OR 248598005 |Sputum volume (observable entity)| OR 442349007 |Venous oxygen saturation (observable entity)|`,
              value: '',
              note: 'Clinical measurements involve the performance of physiological tests to diagnose and refine therapeutic management of an already established disease.'
            },
            'CV19-ASS-ClinicalMeasurementsSubtypes' : {
              title: 'Clinical Measurements with subtypes',
              type: 'autocomplete',
              ecl: `<< 413384008 |Ability to perform cognitive activity (observable entity)| OR << 442476006 |Arterial oxygen saturation (observable entity)| OR << 386725007 |Body temperature (observable entity)| OR << 75367002 |Blood pressure (observable entity)| OR << 248592006 |Character of cough (observable entity)| OR << 364533002 |ColOR << of skin (observable entity)| OR << 709017009 |Compliance behaviOR << to safety precaution (observable entity)| OR << 263731006 |Coughing (observable entity)| OR << 271626009 |Depth of respiration (observable entity)| OR << 226379006 |Food intake (observable entity)| OR << 364075005 |Heart rate (observable entity)| OR << 103228002 |Hemoglobin saturation with oxygen (observable entity)| OR << 6942003 |Level of consciousness (observable entity)| OR << 440398005 |Level of fatigue (observable entity)| OR << 405161002 |Pain level (observable entity)| OR << 248750004 |Peripheral blood flow (observable entity)| OR << 431314004 |Peripheral oxygen saturation (observable entity)| OR << 8499008 |Pulse, function (observable entity)| OR << 248565000 |Respiratory effort (observable entity)| OR << 86290005 |Respiratory rate (observable entity)| OR << 248582003 |Rhythm of respiration (observable entity)| OR << 247433003 |Skin elasticity (observable entity)| OR << 248598005 |Sputum volume (observable entity)| OR << 442349007 |Venous oxygen saturation (observable entity)|`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected Clinical Measurements codes.'
            },
            'CV19-ASS-ClinicalExaminationFindings' : {
              title: 'Clinical Examination Findings',
              type: 'dropdown',
              ecl: ` 20262006 OR 77427003 OR 288848001 OR 371632003 OR 193894004 OR 443371007 OR 126664009 OR 442646005 OR 419045004 OR 274710003 OR 91175000 OR 271823003 OR 288849009 OR  58840004 OR  9763007 OR  386661006 OR  75252003 OR  3424008 OR 70944005 OR 67750007`,
              value: '',
              note: 'Clinical examination findings are the result of measuring, questioning, evaluating, or otherwise examining a patient in healthcare. The following clinical findings are positive or negative indications that signs are present in a COVID-19 patient.'
            },
            'CV19-ASS-ClinicalExaminationFindingsSubtypes' : {
              title: 'Clinical Examination Findings with subtypes',
              type: 'autocomplete',
              ecl: `<< 20262006 OR <<77427003 OR <<288848001 OR <<371632003 OR <<193894004 OR <<443371007 OR <<126664009 OR <<442646005 OR <<419045004 OR <<274710003 OR <<91175000 OR <<271823003 OR <<288849009 OR << 58840004 OR << 9763007 OR << 386661006 OR << 75252003 OR << 3424008 OR <<70944005 OR <<67750007`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected Clinical Examination codes.'
            },
            'CV19-ASS-Diagnosis' : {
              title: 'Diagnosis',
              type: 'radiobutton',
              ecl: `1119302008 OR 189486241000119100 OR 840539006 OR 688232241000119100 OR 840544004`,
              value: '',
              note: 'A diagnosis is the identification of the nature of the illness, based on an examination of the symptoms, observations, measurements, test results and other investigation results. The following subset includes a range of diagnoses directly related to COVID-19.'
            },
            'CV19-ASS-DiagnosisPresentCertainty' : {
              title: 'Diagnosis Present Certainty',
              type: 'radiobutton',
              ecl: `410605003 OR 410592001 OR 415684004`,
              value: '',
              note: 'To represent the certainty of a positive COVID-19 diagnosis, the following subset may be used in conjunction with a diagnosis of  840539006 |COVID-19|.'
            },
            'CV19-ASS-DiagnosisAbsentCertainty' : {
              title: 'Diagnosis Absent Certainty',
              type: 'radiobutton',
              ecl: `410594000 OR 410593006`,
              value: '',
              note: 'To represent the certainty of the absence of COVID-19, the following subset may be used in conjunction with a diagnosis of  688232241000119100 |Disease caused by severe acute respiratory syndrome coronavirus 2 absent (situation)|.'
            },
            'CV19-ASS-ClinicalHistory' : {
              title: 'Clinical History',
              type: 'dropdown',
              ecl: `292508471000119105`,
              value: '',
              note: 'The clinical history concepts are used to record information gained by a physician by asking specific questions, either of the patient or of other people who know the person and can give suitable information, with the aim of obtaining information useful in formulating a diagnosis and providing medical care to the patient.'
            },
            'CV19-ASS-SecondaryConditionsAndComplications' : {
              title: 'Secondary Conditions and Complications',
              type: 'dropdown',
              ecl: `138389411000119105 OR 870590002 OR 870589006 OR 373895009 OR 67782005 OR 674814021000119106 OR 64779008 OR 119731000146105 OR 62914000 OR 1119304009 OR 119741000146102 OR 710027002 OR 419099009 OR 128053003 OR 34095006 OR 67406007 OR 3006004 OR 119981000146107 OR 1240561000000108 OR 119751000146104 OR 25374005 OR 56265001 OR 80394007 OR 389086002 OR 70944005 OR 1240541000000107 OR 90708001 OR 880529761000119102 OR 866151004 OR 59455009 OR 57653000 OR 1240531000000103 OR 1240521000000100 OR 183676005 OR 882784691000119100 OR 1119303003 OR 870591003 OR 302846007 OR 308906005 OR 91302008 OR 870588003 OR 76571007 OR 415623008 OR 238149007 OR 866152006 OR 429340002 OR 75570004 OR 1017214008`,
              value: '',
              note: `A secondary condition is any additional health condition that occurs as a result of the natural progression or expected outcome of the primary condition. A complication is "a disorder caused by another disorder, procedure or event, which is not a natural progression or expected outcome of its cause 5.`
            },
            'CV19-ASS-SecondaryConditionsAndComplicationsSubtypes' : {
              title: 'Secondary Conditions and Complications with subtypes',
              type: 'autocomplete',
              ecl: `<< 138389411000119105 OR << 870590002 OR << 870589006 OR << 373895009 OR << 67782005 OR << 674814021000119106 OR << 64779008 OR << 119731000146105 OR << 62914000 OR << 1119304009 OR << 119741000146102 OR << 710027002 OR << 419099009 OR << 128053003 OR << 34095006 OR << 67406007 OR << 3006004 OR << 119981000146107 OR << 1240561000000108 OR << 119751000146104 OR << 25374005 OR << 56265001 OR << 80394007 OR << 389086002 OR << 70944005 OR << 1240541000000107 OR << 90708001 OR << 880529761000119102 OR << 866151004 OR << 59455009 OR << 57653000 OR << 1240531000000103 OR << 1240521000000100 OR << 183676005 OR << 882784691000119100 OR << 1119303003 OR << 870591003 OR << 302846007 OR << 308906005 OR << 91302008 OR << 870588003 OR << 76571007 OR << 415623008 OR << 238149007 OR << 866152006 OR << 429340002 OR << 75570004 OR << 1017214008`,
              value: '',
              note: `If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected Secondary Conditions and Complications codes.`
            },
            'CV19-ASS-RiskFactors' : {
              title: 'Risk Factors',
              type: 'dropdown',
              ecl: `78648007 OR 27624003 OR 49601007 OR 414029004 OR 235856003 OR 118940003 OR 50043002 OR 8517006 OR 80141007 OR 107921000119107 OR 90708001 OR 363346000 OR 75934005 OR 414825006 OR 414915002 OR 370388006 OR 370391006 OR 77386006 OR 77176002`,
              value: '',
              note: `A secondary condition is any additional health condition that occurs as a result of the natural progression or expected outcome of the primary condition. A complication is "a disorder caused by another disorder, procedure or event, which is not a natural progression or expected outcome of its cause 5.`
            },
            'CV19-ASS-RiskFactorsSubtypes' : {
              title: 'Risk Factors with subtypes',
              type: 'autocomplete',
              ecl: `<< 78648007 OR << 27624003 OR << 49601007 OR << 414029004 OR << 235856003 OR << 118940003 OR << 50043002 OR << 8517006 OR << 80141007 OR << 107921000119107 OR << 90708001 OR << 363346000 OR << 75934005 OR << 414825006 OR << 414915002 OR << 370388006 OR << 370391006 OR << 77386006 OR << 77176002`,
              value: '',
              note: `If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.`
            },
            'CV19-ASS-ProcedureRiskFactors' : {
              title: 'Procedure Risk Factors',
              type: 'dropdown',
              ecl: `367336001 OR 788751009 OR 234336002 OR 86553008 OR 76334006 OR 108290001 OR 312235007 OR 265764009 OR 313039003`,
              value: '',
              note: `Similarly, procedures performed on the patient within a clinically relevant timeframe may also influence the course or outcomes of the COVID-19 disease. The following procedures may be recorded as potential risk factors.`
            },
            'CV19-ASS-ProcedureRiskFactorsSubtypes' : {
              title: 'Procedure Risk Factors with subtypes',
              type: 'autocomplete',
              ecl: `<<367336001 OR <<788751009 OR <<234336002 OR <<86553008 OR <<76334006 OR <<108290001 OR <<312235007 OR <<265764009 OR <<313039003`,
              value: '',
              note: `If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.`
            },
            'CV19-ASS-Comorbidities' : {
              title: 'Comorbidities',
              type: 'autocomplete',
              ecl: `<  64572001 |Disease (disorder)|`,
              value: '',
              note: `Comorbidities are additional medical conditions of concern. The following SNOMED CT subset can be used to record comorbidities.`
            },
            'CV19-ASS-ExposureEvent' : {
              title: 'Exposure Event',
              type: 'dropdown',
              ecl: `840546002 |Exposure to severe acute respiratory syndrome coronavirus 2 (event)| OR 897036007 |Occupational exposure to Severe acute respiratory syndrome coronavirus 2 (event)|`,
              value: '',
              note: `An exposure event is when an individual comes into close contact with an infected person's contaminated droplets, carried in the air or on contaminated hands, surfaces or other objects. The subset below includes SNOMED CT events for recording exposure to COVID-19.`
            },
            'CV19-ASS-ExposureFinding' : {
              title: 'Exposure Finding',
              type: 'dropdown',
              ecl: `870577009	|At increased risk of exposure to severe acute respiratory syndrome coronavirus 2 (finding)|`,
              value: '',
              note: `An exposure finding is the result of an observation or evaluation that relates to the patient's exposure to an infection. The subset below includes SNOMED CT findings relating to COVID-19 exposure.`
            },
            'CV19-ASS-Organism' : {
              title: 'Organism',
              type: 'dropdown',
              ecl: `840533007	|Severe acute respiratory syndrome coronavirus 2 (organism)|`,
              value: '',
              note: `The following organism should be used when recording the organism causing COVID-19 infections.`
            },
            'CV19-ASS-ViralCoInfections' : {
              title: 'Viral CoInfections',
              type: 'autocomplete',
              ecl: `<< 49872002 |Virus (organism)|  MINUS  840533007 |Severe acute respiratory syndrome coronavirus 2 (organism)|`,
              value: '',
              note: `The following organisms can be used to record the cause of related viral co-infections.`
            }
          }
        },
        'CV19-INV': {
          title: 'Tests and Investigations',
          note: 'Laboratory testing for COVID-19 and the associated SARS-CoV-2 virus includes methods that detect the presence of the virus and antibodies produced in response to infection, in specimen samples taken from potentially infected individuals. ',
          bindings: {
            'CV19-INV-Specimen' : {
              title: 'Specimen',
              type: 'dropdown',
              ecl: `697989009 |Anterior nares swab (specimen)| OR 119297000 |Blood specimen (specimen)| OR 258607008 |Bronchoalveolar lavage fluid sample (specimen)| OR 122554006 |Capillary blood specimen (specimen)| OR 119294007 |Dried blood specimen (specimen)| OR 168137004 |Gastric aspirate sample (specimen)| OR 258606004 |Lower respiratory sample (specimen)| OR 258411007 |Nasopharyngeal aspirate (specimen)| OR 258500001 |Nasopharyngeal swab (specimen)| OR 258412000 |Oropharyngeal aspirate (specimen)| OR 418564007 |Pleural fluid specimen (specimen)| OR 119342007 |Saliva specimen (specimen)| OR 119364003 |Serum specimen (specimen)| OR 734427005 |Specimen from duodenum obtained by aspiration (specimen)| OR 122610009 |Specimen from lung obtained by biopsy (specimen)| OR 445447003 |Specimen from trachea obtained by aspiration (specimen)| OR 119334006 |Sputum specimen (specimen)| OR 119339001 |Stool specimen (specimen)| OR 472901003 |Swab from nasal sinus (specimen)| OR 445297001 |Swab of internal nose (specimen)| OR 871810001 |Swab specimen from nasal mid-turbinate (specimen)| OR 461911000124106 |Swab specimen from oropharynx (specimen)| OR 258529004 |Throat swab (specimen)| OR 122877000 |Upper respiratory fluid specimen obtained by tracheal aspiration (specimen)| OR 309164002 |Upper respiratory swab sample (specimen)| OR 122575003 |Urine specimen (specimen)| OR 258580003 |Whole blood sample (specimen)|`,
              value: '',
              note: 'Specimens are samples of substances taken from an individual for testing - for example 258500001 |Nasopharyngeal swab (specimen)| or 119339001 |Stool specimen (specimen)|. The following specimens may be relevant to COVID-19 testing in one or more countries affected by the virus.'
            },
            'CV19-INV-SpecimenSubtypes' : {
              title: 'Specimen with subtypes',
              type: 'autocomplete',
              ecl: `<< 697989009 |Anterior nares swab (specimen)| OR << 119297000 |Blood specimen (specimen)| OR << 258607008 |Bronchoalveolar lavage fluid sample (specimen)| OR << 122554006 |Capillary blood specimen (specimen)| OR << 119294007 |Dried blood specimen (specimen)| OR << 168137004 |Gastric aspirate sample (specimen)| OR << 258606004 |Lower respiratory sample (specimen)| OR << 258411007 |Nasopharyngeal aspirate (specimen)| OR << 258500001 |Nasopharyngeal swab (specimen)| OR << 258412000 |Oropharyngeal aspirate (specimen)| OR << 418564007 |Pleural fluid specimen (specimen)| OR << 119342007 |Saliva specimen (specimen)| OR << 119364003 |Serum specimen (specimen)| OR << 734427005 |Specimen from duodenum obtained by aspiration (specimen)| OR << 122610009 |Specimen from lung obtained by biopsy (specimen)| OR << 445447003 |Specimen from trachea obtained by aspiration (specimen)| OR << 119334006 |Sputum specimen (specimen)| OR << 119339001 |Stool specimen (specimen)| OR << 472901003 |Swab from nasal sinus (specimen)| OR << 445297001 |Swab of internal nose (specimen)| OR << 871810001 |Swab specimen from nasal mid-turbinate (specimen)| OR << 461911000124106 |Swab specimen from oropharynx (specimen)| OR << 258529004 |Throat swab (specimen)| OR << 122877000 |Upper respiratory fluid specimen obtained by tracheal aspiration (specimen)| OR << 309164002 |Upper respiratory swab sample (specimen)| OR << 122575003 |Urine specimen (specimen)| OR << 258580003 |Whole blood sample (specimen)|`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.'
            },
            'CV19-INV-LaboratoryTests' : {
              title: 'Laboratory Tests',
              type: 'dropdown',
              ecl: `444077007 OR 76978006 OR 30662005 OR 414464004 OR 122435008 OR 9718006 OR 122433001 OR 118147004 OR 88667002 OR  60170009 OR  61911006 OR  871555000 OR  871559006 OR  871556004 OR  871557008 OR  871558003 OR  871560001 OR  1240461000000109 OR  1240471000000102`,
              value: '',
              note: 'Laboratory tests can be performed to detect the presence of the COVID-19 virus and antibodies produced in response to this virus. The following SNOMED CT concepts can be used to record laboratory tests that may be performed during the investigation process. '
            },
            'CV19-INV-LaboratoryTestsSubtypes' : {
              title: 'Laboratory Tests with subtypes',
              type: 'autocomplete',
              ecl: `<<444077007 OR <<76978006 OR <<30662005 OR <<414464004 OR <<122435008 OR <<9718006 OR <<122433001 OR <<118147004 OR <<88667002 OR << 60170009 OR << 61911006 OR << 871555000 OR << 871559006 OR << 871556004 OR << 871557008 OR << 871558003 OR << 871560001 OR << 1240461000000109 OR << 1240471000000102`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.'
            },
            'CV19-INV-RadiologyProcedures' : {
              title: 'Radiology Procedures',
              type: 'dropdown',
              ecl: `16332841000119106 OR 169069000 OR 399208008 OR 88628008`,
              value: '',
              note: 'Radiology procedures are procedures that obtain images of the inside of the body. The following SNOMED CT concepts may be used to record radiology procedures that may be performed during the investigation and treatment of suspected or confirmed Covid-19 patients.'
            },
            'CV19-INV-RadiologyProceduresSubtypes' : {
              title: 'Radiology Procedures with subtypes',
              type: 'autocomplete',
              ecl: `<< 16332841000119106 OR << 169069000 OR << 399208008 OR << 88628008`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.'
            },
            'CV19-INV-SubstancesAndVirus' : {
              title: 'Substances and Virus',
              type: 'dropdown',
              ecl: `840535000 OR 840536004 OR 840533007 OR  870361009 OR  870362002 OR  1240411000000107 OR 1119343008 OR  1155866009`,
              value: '',
              note: 'An antibody is a protective protein produced by the immune system in response to the presence of a foreign substance, called an antigen. The following antibody/antigen and virus organism concepts can be used to record the focus of a COVID-19 investigation.'
            },
            'CV19-INV-SubstancesAndVirusSubtypes' : {
              title: 'Substances and Virus with subtypes',
              type: 'autocomplete',
              ecl: `<<840535000 OR <<840536004 OR <<840533007 OR << 870361009 OR << 870362002 OR << 1240411000000107 OR <<1119343008 OR << 1155866009`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.'
            },
            'CV19-INV-LabTestResults' : {
              title: 'Laboratory Test Results',
              type: 'dropdown',
              ecl: `260373001 OR 260415000 OR 419984006 OR 125154007 OR  895231008 OR  1240581000000104 OR  1240591000000102 OR  897035006 OR  897034005`,
              value: '',
              note: 'Laboratory test results are used to specify whether the Laboratory test focus was detected or not detected following the conducted laboratory test. The following qualifier values may be used for this purpose.'
            },
            'CV19-INV-LabTestResultsSubtypes' : {
              title: 'Laboratory Test Results with subtypes',
              type: 'autocomplete',
              ecl: `<<260373001 OR <<260415000 OR <<419984006 OR <<125154007 OR << 895231008 OR << 1240581000000104 OR << 1240591000000102 OR << 897035006 OR << 897034005`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.'
            },
            'CV19-INV-InvestigationProcedures' : {
              title: 'Investigation Procedures',
              type: 'autocomplete',
              ecl: `<  386053000 |Evaluation procedure (procedure)|
                    OR <  308335008 |Patient encounter procedure (procedure)|
                    OR <  17636008 |Specimen collection (procedure)|`,
              value: '',
              note: 'Other investigative procedures - e.g. 719410009 |Consultation via video conference (procedure)| and  386472008 |Telephone consultation (procedure)| and  243791004 |Viral screening (procedure)|  and 29303009 |Electrocardiographic procedure (procedure)|- may be performed to assess a COVID-19 patient. The following subset represents procedures that can be performed during the clinical assessment process.'
            },
            'CV19-INV-InvestigationFindings' : {
              title: 'Investigation Findings',
              type: 'autocomplete',
              ecl: `<  313333008 |Sample obtained (situation)|
                    OR <  168123008 |Sample sent for examination (situation)|`,
              value: '',
              note: 'The following subset represents a collection of findings with context that may be related to these investigations and captured using SNOMED CT - for example  269944001 |Nasal swab taken (situation)| or  168331006 |Blood sent for virology (situation)|.'
            }
          }
        },
        'CV19-PTE': {
          title: 'Prevention, Treatment and Education',
          note: 'Preventing the potential spread of COVID-19 can involve a range of precautions, such as isolation and the wearing of personal protective equipment, and education to patients, healthcare workers and the broader community. Once COVID-19 is diagnosed, various treatments may be considered, together with education for the patient and their families/caregivers. ',
          bindings: {
            'CV19-INV-PreventionProcedures' : {
              title: 'Prevention Procedures',
              type: 'dropdown',
              ecl: `1157196000 OR 1144997007 OR 1157197009 OR 1157108008 OR 1144998002 OR  1162646009 OR  1156257007 OR  1157107003 OR 840534001 OR 1119350007 OR  1162645008 OR 409524006 OR 422181004 OR 449399005 OR 386242000 OR 225368008 OR 409529001 OR 409686004 OR 449390009 OR 122467006 OR 77248004 OR 370832005 OR 361235007 OR 170499009 OR 170500000 OR 40174006 OR 225995005 OR 275827007 OR 38883000 OR 29537000 OR 736762004 OR 409525007 OR 410410006 OR 409522005 OR 409684001 OR 409583003 OR 243791004`,
              value: '',
              note: 'Precautionary measures, such as  840534001 |SARS-CoV-2 vaccination|,  225368008 |Contact tracing|, and  170499009 |Isolation of infection contact (procedure)| are extremely important in the fight against infectious diseases such as COVID-19. The following preventative measures can be recorded using SNOMED CT.'
            },
            'CV19-INV-PreventionProceduresSubtypes' : {
              title: 'Prevention Procedures with subtypes',
              type: 'autocomplete',
              ecl: `<<1157196000 OR <<1144997007 OR <<1157197009 OR <<1157108008 OR <<1144998002 OR << 1162646009 OR << 1156257007 OR << 1157107003 OR <<840534001 OR <<1119350007 OR << 1162645008 OR <<409524006 OR <<422181004 OR <<449399005 OR <<386242000 OR <<225368008 OR <<409529001 OR <<409686004 OR <<449390009 OR <<122467006 OR <<77248004 OR <<370832005 OR <<361235007 OR <<170499009 OR <<170500000 OR <<40174006 OR <<225995005 OR <<275827007 OR <<38883000 OR <<29537000 OR <<736762004 OR <<409525007 OR <<410410006 OR <<409522005 OR <<409684001 OR <<409583003 OR <<243791004`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.'
            },
            'CV19-INV-PreventionFindings' : {
              title: 'Prevention Findings',
              type: 'dropdown',
              ecl: ` 1142182006 OR  1142181004 OR  1145003007 OR  1145035007 OR  1145034006 OR  1145023008 OR  1145033000 OR  1145030002 OR  1145022003 OR  1145032005 OR  1145029007 OR 1157195001 OR 1157106007 OR 1162644007 OR 1157198004 OR 1157200005 OR 1157199007 OR 1157201009 OR 1157113007 OR 1157120000 OR 1162648005 OR 1162650002 OR 1157110005 OR 1157118003 OR 1162647000 OR 1162649002 OR 1156270003 OR 1156746003 OR 1156256003 OR 1156267002 OR 1156265005`,
              value: '',
              note: 'Clinical findings (or situations) relating to the COVID-19 preventative measures performed may be captured in a health record. The following prevention findings (or situations) may be recorded using SNOMED CT.'
            },
            'CV19-INV-PreventionFindingsSubtypes' : {
              title: 'Prevention Findings with subtypes',
              type: 'autocomplete',
              ecl: `<< 1142182006 OR << 1142181004 OR << 1145003007 OR << 1145035007 OR << 1145034006 OR << 1145023008 OR << 1145033000 OR << 1145030002 OR << 1145022003 OR << 1145032005 OR << 1145029007 OR <<1157195001 OR <<1157106007 OR <<1162644007 OR <<1157198004 OR <<1157200005 OR <<1157199007 OR <<1157201009 OR <<1157113007 OR <<1157120000 OR <<1162648005 OR <<1162650002 OR <<1157110005 OR <<1157118003 OR <<1162647000 OR <<1162649002 OR <<1156270003 OR <<1156746003 OR <<1156256003 OR <<1156267002 OR <<1156265005`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.'
            },
            'CV19-INV-Education' : {
              title: 'Education',
              type: 'dropdown',
              ecl: `698608004 OR 385820004 OR  736017006 OR  223426009 OR  868263005`,
              value: '',
              note: 'Education is an important aspect of preventing and managing COVID-19 infection. The following SNOMED CT concepts may be used to record this data.'
            },
            'CV19-INV-EducationSubtypes' : {
              title: 'Education with subtypes',
              type: 'autocomplete',
              ecl: `<<698608004 OR <<385820004 OR << 736017006 OR << 223426009 OR << 868263005`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.'
            },
            'CV19-INV-Vaccines' : {
              title: 'Vaccines',
              type: 'dropdown',
              ecl: `28531000087107 OR 1157024006 OR  29061000087103 OR  1119305005 OR  1119349007 OR 1162643001`,
              value: '',
              note: 'A number of countries are trialling the use of medication to treat COVID-19. Many of these medications can be found in the  373873005 |Pharmaceutical / biologic product (product)| hierarchy of SNOMED CT. However until there is sufficient evidence, the WHO is cautioning against associations recommending or administering these unproven treatments to patients with COVID-19 or people self-medicating with them. For this reason, we have not included a medication treatment subset at this time. COVID-19 vaccines, however, have been proven to be effective in clinical trials. The following generic vaccine concepts may be recorded using SNOMED CT.'
            },
            'CV19-INV-AdministrativeProcedures' : {
              title: 'Administrative Procedures',
              type: 'dropdown',
              ecl: `183452005 OR 305376003 OR 305351004 OR 305360007 OR 32485007 OR 417005 OR  107724000 OR  397821002 OR  710112000 OR  429202003`,
              value: '',
              note: 'Patients with severe symptoms or complications may require admission to hospital, or other administrative procedures. The following administrative procedures may be recorded using SNOMED CT.'
            },
            'CV19-INV-AdministrativeProceduresSubtypes' : {
              title: 'Administrative Procedures with subtypes',
              type: 'autocomplete',
              ecl: `<<183452005 OR <<305376003 OR <<305351004 OR <<305360007 OR <<32485007 OR <<417005 OR << 107724000 OR << 397821002 OR << 710112000 OR << 429202003`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.'
            },
            'CV19-INV-TherapeuticProcedures' : {
              title: 'Therapeutic Procedures',
              type: 'dropdown',
              ecl: `40617009 OR 233573008 OR 447996002 OR 243141005 OR 428311008 OR 371908008 OR 371907003 OR 11140008 OR  229301009 OR  431182000 OR  697984004 OR  829831000000100 OR 1156748002 OR 1156747007`,
              value: '',
              note: 'Patients suffering from severe COVID-19 symptoms or complications may require a range of therapeutic procedures, many of them involving supportive care to help them breathe. The following therapeutic procedures may be recorded using SNOMED CT.'
            },
            'CV19-INV-TherapeuticProceduresSubtypes' : {
              title: 'Therapeutic Procedures with subtypes',
              type: 'autocomplete',
              ecl: `<<40617009 OR <<233573008 OR <<447996002 OR <<243141005 OR <<428311008 OR <<371908008 OR <<371907003 OR <<11140008 OR << 229301009 OR << 431182000 OR << 697984004 OR << 829831000000100 OR <<1156748002 OR <<1156747007`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.'
            },
            'CV19-INV-TreatmentPreventionFindings' : {
              title: 'Treatment Prevention Findings',
              type: 'dropdown',
              ecl: ` 718447001 OR 79031000119101 OR 931000119107 OR 444932008 OR 763326004 OR 419991009 OR 405496006 OR 22803001 OR 718085007 OR 128258000 OR  371825009 OR  371820004`,
              value: '',
              note: 'Clinical findings relating to the treatment performed on a patient may be captured in a health record. The following treatment findings may be recorded using SNOMED CT.'
            },
            'CV19-INV-TreatmentPreventionFindingsSubtypes' : {
              title: 'Treatment Prevention Findings with subtypes',
              type: 'autocomplete',
              ecl: `<< 718447001 OR <<79031000119101 OR <<931000119107 OR <<444932008 OR <<763326004 OR <<419991009 OR <<405496006 OR <<22803001 OR <<718085007 OR <<128258000 OR << 371825009 OR << 371820004`,
              value: '',
              note: 'If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected codes.'
            },
            'CV19-INV-TreatmentEquipment' : {
              title: 'Treatment Equipment',
              type: 'autocomplete',
              ecl: `<  277973009 |Respiratory equipment (physical object)|  
                    OR <  336589003 |Oxygen equipment (physical object)|  
                    OR <  334943003 |Respiratory appliances (physical object)|  
                    OR <  304077006 |Respiratory system device (physical object)|  
                    OR <  706167001 |Anesthesia and respiratory device (physical object)|`,
              value: '',
              note: 'Respiratory equipment includes those medical devices used to support breathing and respiratory function - for example 706173000 |Intensive-care ventilator (physical object)| , and 464328001 |Thoracic cannula (physical object)| .'
            }
          }
        }
      }
  }),
};
</script>
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
        <h1>COVID-19 Coding Demonstrator</h1>
      </div>
      <v-spacer></v-spacer>
      <v-btn color="success">
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
        this.bindings.push({ section: this.sections[section].title, title: this.sections[section].bindings[binding].title, ecl: this.sections[section].bindings[binding].ecl })
      }
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
              note: `If the implementation uses a terminology server, the binding can be implemented including all subtypes of the selected Secondary Conditions and Complications codes.`
            }
          }
        },
        'CV19-INV': {
          title: 'Tests and Investigations',
          note: 'Laboratory testing for COVID-19 and the associated SARS-CoV-2 virus includes methods that detect the presence of the virus and antibodies produced in response to infection, in specimen samples taken from potentially infected individuals. ',
          bindings: {
            'CV19-ASS-Symptoms' : {
              title: 'Biological Sex',
              type: 'dropdown',
              ecl: `<  429019009 |Finding related to biological sex (finding)|  
                    MINUS <<  302081005 |Finding of sex of baby (finding)|`,
              value: '',
              note: 'Multiple types of sex and/or gender are recorded in clinical practice, including biological sex and gender identity.'
            }
          }
        },
        'CV19-PTE': {
          title: 'Prevention, Treatment and Education',
          note: 'Preventing the potential spread of COVID-19 can involve a range of precautions, such as isolation and the wearing of personal protective equipment, and education to patients, healthcare workers and the broader community. Once COVID-19 is diagnosed, various treatments may be considered, together with education for the patient and their families/caregivers. ',
          bindings: {
            'CV19-PTE-Education' : {
              title: 'Education',
              type: 'radiobutton',
              ecl: `736017006	|Education about hand hygiene (procedure)|
              OR 868263005	|Education about infectious disease (procedure)|
              OR 223426009	|Equipment use education (procedure)|
              OR 698608004	|Hand washing education (procedure)|
              OR 385820004	|Infection control education (procedure)|`,
              value: '',
              note: 'Education is an important aspect of preventing and managing COVID-19 infection.'
            }
          }
        }
      }
  }),
};
</script>
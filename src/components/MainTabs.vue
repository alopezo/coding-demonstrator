<template>
  <v-container>
    <v-tabs>
      <v-tab v-for="(section, name) in sections" :key="name" :href="'#'+name">
        {{ section.title }}
      </v-tab>
      <v-tab-item v-for="(section, name) in sections" :key="name" :value="name">
        <SectionPanel :title="section.title" :note="section.note" :bindings="section.bindings"/>
      </v-tab-item>
    </v-tabs>
    <v-tabs-items>
    </v-tabs-items>
  </v-container>
</template>

<script>
  import SectionPanel from './SectionPanel.vue';


  export default {
    name: 'MainTabs',
    data: () => ({
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
              title: 'Biological Sex',
              type: 'dropdown',
              ecl: `<  429019009 |Finding related to biological sex (finding)|  
                    MINUS <<  302081005 |Finding of sex of baby (finding)|`,
              value: '',
              note: 'Multiple types of sex and/or gender are recorded in clinical practice, including biological sex and gender identity.'
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
    components: {
      SectionPanel
    },
    methods: {
    }
  }
</script>
<style scoped>
  /* h1 {
    margin-top: 100px;
  } */
</style>
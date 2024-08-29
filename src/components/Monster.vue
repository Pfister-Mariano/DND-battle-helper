<template>
    <article class="monsterCard">
        <Overlay v-if="dmgModal" @close="dmgModal = false">
            <div class="monsterTable dmgOverlay">
                <div class="monsterTableWrapper">
                    <div class="monsterTableRow" v-for="(dmg, index) in dealingDmg">
                        <input class="monsterTableTime" v-model="dmg.time">

                        <select v-model="dmg.name">
                            <option v-for="dmgType in allDmgTypes">{{ dmgType.replace(' damage', '') }}</option>
                        </select>

                        <!-- <input class="monsterTableName" v-model="dmg.name"> -->

                        <div class="removeStatus" @click="deleteTableRow(index, dealingDmg)"><i
                                class="fa-solid fa-xmark"></i></div>
                    </div>

                    <div class="monsterTableRow addRow" @click="addTableRow(dealingDmg)"><i
                            class="fa-solid fa-plus"></i>
                    </div>
                </div>
                <div class="dealDmgButton" @click="calcDmg(), dmgModal = false">Deal DMG</div>
            </div>
        </Overlay>
        
        <div class="monsterCardSettings">
            <h4><span>{{ props.monsterIndex + 1 }}: </span>{{ props.monster.name }}</h4>
            <div class="monsterCardControls">
                <div>
                    <span class="controlDmg" @click="dmgModal = true">DMG</span>
                    <!--<span class="controlHeal">HEAL</span>-->
                </div>
                <div>
                    <!-- <span class="controlEdit">EDIT</span> -->
                    <span class="controlCopy" @click="copyCurrentMonster();">COPY</span>
                </div>
            </div>
        </div>

        <div class="monsterTabSettings">
            <span @click="currentMonsterTab = 'hp'" :class="{ 'active': currentMonsterTab === 'hp' }">HP</span>
            <span @click="currentMonsterTab = 'stats'" :class="{ 'active': currentMonsterTab === 'stats' }">Stats</span>
            <span @click="currentMonsterTab = 'actions'" :class="{ 'active': currentMonsterTab === 'actions' }">Actions</span>
            <span @click="currentMonsterTab = 'passive'" :class="{ 'active': currentMonsterTab === 'passive' }">Passive</span>
        </div>
        
        <div class="monsterMain">
            <div class="monsterMainTab hpTab" v-if="currentMonsterTab === 'hp'">
                <div class="monsterHpBar">
                    <div class="monsterHp">
                        <span>HP</span>
                        <span><input type="text" v-model="currentHp" maxlength="4"> / <input type="text" v-model="maxHp"
                                maxlength="4"></span>
                    </div>
                    <div class="monsterSpeed">
                        <span>Speed</span>
                        <p>Walk: <input type="text" v-model="walkSpeed"></p>
                        <p>Climb: <input type="text" v-model="climbSpeed"></p>
                        <p>Swim: <input type="text" v-model="swimSpeed"></p>
                        <p>Fly: <input type="text" v-model="flySpeed"></p>
                    </div>
                    <div class="monsterAc">
                        <span>AC</span>
                        <span><input type="text" maxlength="2" v-model="currentAc"></span>
                    </div>
                </div>
                <div class="monsterTable">
                    <div class="monsterTableWrapper">
                        <div class="monsterTableRow" v-for="(note, index) in notes">
                            <input class="monsterTableTime" v-model="note.time" maxlength="2">
                            <input class="monsterTableName" v-model="note.name">
                            <div class="removeStatus" @click="deleteTableRow(index, notes)"><i
                                    class="fa-solid fa-xmark"></i></div>
                        </div>
                        <div class="monsterTableRow addRow" @click="addTableRow(notes)"><i class="fa-solid fa-plus"></i>
                        </div>
                    </div>
                </div>
            </div>
            <div class="monsterMainTab statsTab" v-if="currentMonsterTab === 'stats'">
                <div class="monsterStats">
                    <div class="statBlock" v-for="stat in statTable">
                        <span>{{ stat.name }}</span>
                        <span><input type="text" maxlength="2" v-model="stat.modifier"> / <input type="text"
                                maxlength="2" v-model="stat.score"></span>
                        <div class="doubleRoll advantage" v-if="stat.extra === true">ADV</div>
                        <div class="doubleRoll disadvantage" v-else-if="stat.extra === false">DIS</div>
                    </div>
                </div>
                <div class="monsterTable skillTable">
                    <div class="monsterTableWrapper">
                        <div class="monsterTableRow" v-for="(skill, index) in skills">
                            <input :class="skill.type === 'save' ? 'monsterTableTime savingThrow' : 'monsterTableTime'"
                                v-model="skill.time" maxlength="3">
                            <input class="monsterTableName" v-model="skill.name">
                            <div class="removeStatus" @click="deleteTableRow(index, skills)"><i
                                    class="fa-solid fa-xmark"></i></div>
                        </div>
                        <div class="monsterTableRow addRow" @click="addTableRow(skills)"><i
                                class="fa-solid fa-plus"></i></div>
                    </div>
                </div>
            </div>
            <div class="monsterMainTab actionsTab" v-if="currentMonsterTab === 'actions'">
                <div class="monsterSpellcasting">
                    <div class="spellcastingOverlayButton" @click="spellcastingModal = true">Spellcasting</div>
                    <Overlay v-if="spellcastingModal" @close="spellcastingModal = false">
                        <div class="spelcastingInfo">
                            <h5>Modifier: {{ props.monster.spellcasting[0].ability.toUpperCase() }}</h5><br>
                        </div>
                        <div class="monsterTable availableSpells">
                            <div class="spellGroup" v-for="(spellGroup, index) in props.monster.spellcasting[0].daily">
                                <div class="monsterTableRow titleRow">
                                    <div class="spellGroupTitle">{{ index.charAt(0) }}x/per day</div>
                                </div>
                                <div class="monsterTableRow" v-for="spell in spellGroup">{{ spell.replaceAll('{@spell ',
                                    '').replaceAll('}', '') }}</div>
                            </div>
                            <div class="monsterTableRow titleRow" v-if="props.monster.spellcasting[0].will">
                                <div class="spellGroupTitle">At will</div>
                            </div>
                            <div class="monsterTableRow" v-for="spell in props.monster.spellcasting[0].will">{{
                                spell.replaceAll('{@spell ', '').replaceAll('}', '') }}</div>
                        </div>
                    </Overlay>
                </div>
                <div class="monsterTable actionTable">
                    <div class="monsterTableWrapper">
                        <div class="monsterTableRow" v-for="(action, index) in actions">

                            <div class="monsterTableTime" v-if="action.type === 'row'">
                                <i class="fa-solid fa-dice-d20"></i>
                            </div>

                            <div class="monsterTableTime actionTitle" v-else-if="action.type === 'title'">{{ action.name
                                }}</div>

                            <div class="monsterTableName" v-if="action.type === 'row'" v-html="action.name"></div>

                            <!-- <div class="removeStatus" @click="deleteTableRow(index, actions)"><i class="fa-solid fa-xmark"></i></div> -->
                        </div>
                        <!-- <div class="monsterTableRow addRow" @click="addTableRow(actions)"><i class="fa-solid fa-plus"></i></div> -->
                    </div>
                </div>
            </div>
            <div class="monsterMainTab passiveTab" v-if="currentMonsterTab === 'passive'">
                <div class="passiveTabWrapper">
                    <div class="dmgResistance" v-if="dmgResistance.length > 0">
                        <div class="titleGreen">DMG Resistance</div>
                        <div class="listGreen">
                            <span>{{ typeof dmgResistance === 'string' ? dmgResistance : dmgResistance.join(', ')
                                }}</span>
                        </div>
                    </div>

                    <div class="dmgImmunity" v-if="dmgImmunity.length > 0">
                        <div class="titleGreen">DMG Immunities</div>
                        <div class="listGreen">
                            <span>{{ typeof dmgImmunity === 'string' ? dmgImmunity : dmgImmunity.join(', ') }}</span>
                        </div>
                    </div>

                    <div class="conditionImmunity" v-if="conditionImmune.length > 0">
                        <div class="titleGreen">Condition Immunities</div>
                        <div class="listGreen">
                            <span>{{ typeof conditionImmune === 'string' ? conditionImmune : conditionImmune.join(', ')
                                }}</span>
                        </div>
                    </div>

                    <div class="dmgVulnerability" v-if="vulnerable.length > 0">
                        <div class="titleRed">DMG Vaulnerability</div>
                        <div class="listRed">
                            <span>{{ typeof vulnerable === 'string' ? vulnerable : vulnerable.join(', ') }}</span>
                        </div>
                    </div>

                    <div class="senses" v-if="senses.length > 0">
                        <div class="titleSenses">Senses</div>
                        <div class="listSenses">
                            <span>{{ senses }}</span>
                        </div>
                    </div>

                    <div class="passiveAbility" v-if="traits !== undefined" v-for="trait in traits">
                        <div class="titleAbility">{{ trait.name }}</div>
                        <div class="listAbility">
                            <span>{{ trait.entries[0] }}</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </article>
</template>

<script setup>

import { defineProps, onMounted, ref, defineEmits } from 'vue';
import Overlay from '@/components/Overlay.vue'
import Monster from '@/components/Monster.vue'

const dmgModal = ref(false)
const spellcastingModal = ref(false)

const props = defineProps({
    monster: Object,
    monsterIndex: String
})

const emit = defineEmits(['copyMonster']);

function copyCurrentMonster() {
    emit('copyMonster', props.monster)
}

/* HP TAB   */

const currentMonsterTab = ref('hp')

const currentHp = ref(props.monster.hp.average)
const maxHp = ref(props.monster.hp.average)
let currentAc;

if (typeof props.monster.ac === 'object' && props.monster.ac[0].ac !== undefined) {
    currentAc = ref(props.monster.ac[0].ac)
} else if (typeof props.monster.ac === 'object' && props.monster.ac[0].ac === undefined) {
    currentAc = ref(props.monster.ac[0])
} else {
    currentAc = ref(props.monster.ac)
}

let walkSpeed = ref(props.monster.speed.walk)
let climbSpeed = ref(props.monster.speed.climb)
let swimSpeed = ref(props.monster.speed.swim)
let flySpeed;

if (typeof props.monster.speed.fly === 'object') {
    flySpeed = ref(props.monster.speed.fly.number)
} else {
    flySpeed = ref(props.monster.speed.fly);
}

if (climbSpeed.value === undefined) {
    climbSpeed.value = walkSpeed.value / 2
}

if (swimSpeed.value === undefined) {
    swimSpeed.value = walkSpeed.value / 2
}

const notes = ref([
    {
        time: null,
        name: '',
    },
    {
        time: null,
        name: '',
    },
    {
        time: null,
        name: '',
    },
    {
        time: null,
        name: '',
    },
])

const deleteTableRow = (index, tableContents) => {
    tableContents.splice(index, 1)
}

const addTableRow = (tableContents) => {
    tableContents.push({
        time: null,
        name: '',
    })
}

/* STATS TAB */

let statTable = ref([
    {
        name: 'Strength',
        score: props.monster.str,
        modifier: abilityModifier(props.monster.str),
        extra: null
    },
    {
        name: 'Dexterity',
        score: props.monster.dex,
        modifier: abilityModifier(props.monster.dex),
        // extra: true
        extra: null
    },
    {
        name: 'Constitution',
        score: props.monster.con,
        modifier: abilityModifier(props.monster.con),
        // extra: false
        extra: null
    },
    {
        name: 'Intelligence',
        score: props.monster.int,
        modifier: abilityModifier(props.monster.int),
        extra: null
    },
    {
        name: 'Wisdom',
        score: props.monster.wis,
        modifier: abilityModifier(props.monster.wis),
        extra: null
    },
    {
        name: 'Charisma',
        score: props.monster.cha,
        modifier: abilityModifier(props.monster.cha),
        extra: null
    }
])

function abilityModifier(score) {
    let modifierRaw = Math.floor((score - 10) / 2)

    let modifier = ` ${modifierRaw > 1 ? '+' : ''}${modifierRaw}`

    return modifier
}

let skills = ref([
    // {
    //     time: null,
    //     name: '',
    // },
    // {
    //     time: null,
    //     name: '',
    // },
    // {
    //     time: null,
    //     name: '',
    // }
])

if (props.monster.save !== undefined) {
    for (let [key, value] of Object.entries(props.monster.save)) {
        let saveName = `${key}`

        if (saveName === 'str') {
            saveName = 'Saving Throw: Strength'
        } else if (saveName === 'dex') {
            saveName = 'Saving Throw: Dexterity'
        } else if (saveName === 'con') {
            saveName = 'Saving Throw: Constitution'
        } else if (saveName === 'int') {
            saveName = 'Saving Throw: Intelligence'
        } else if (saveName === 'wis') {
            saveName = 'Saving Throw: Wisdom'
        } else if (saveName === 'cha') {
            saveName = 'Saving Throw: Charisma'
        }

        skills.value.push({
            time: value,
            name: saveName,
            type: 'save'
        })
    }
}

if (props.monster.skill !== undefined) {
    for (let [key, value] of Object.entries(props.monster.skill)) {
        const capitalized = key.charAt(0).toUpperCase() + key.slice(1)
        let skillName = `Skill: ${capitalized}`

        skills.value.push({
            time: value,
            name: skillName,
            type: 'skill'
        })
    }
}

/* ACTIONS TAB */

let actions = ref([
    // {
    //     type: 'row',
    //     name: 'XXX',
    // },
])
const allDmgTypes = [
    "bludgeoning damage",
    "piercing damage",
    "slashing damage",
    "force damage",
    "acid damage",
    "cold damage",
    "fire damage",
    "lightning damage",
    "necrotic damage",
    "poison damage",
    "psychic damage",
    "radiant damage",
    "thunder damage",
];
function splitStringByPlaceholders(text, rechargeOnly = false) {
    let parts = text.split(/(\{@.*?\})/);
    let actionValues = {
        atkType: "",
        bonusToHit: "",
        damage: [],
        rechargeTurns: "",
    }

    let damageRolls = 0;
    let includedDamageType = getSpecificDmgTypes(text, allDmgTypes)

    parts.filter(part => part.trim() !== '').map((part, index) => {
        let match = part.match(/\{@(.*?)\}/);
        if (match) {
            let key = match[1];

            if (key.startsWith('atk ')) {

                let atk = key.split('atk ')[1];
                actionValues.atkType = atk;

            } else if (key.startsWith('hit ')) {

                let hit = key.split('hit ')[1];
                actionValues.bonusToHit = hit;

            } else if (key.startsWith('recharge ')) {

                let recharge = key.split('recharge ')[1];
                actionValues.rechargeTurns = recharge;

            } else if (key.startsWith('damage ')) {

                let damage = key.split('damage ')[1];
                damage = damage.split('d');
                damage[1] = damage[1].split('+');

                let damageValue = {
                    diceNumbers: damage[0],
                    diceType: damage[1][0],
                    diceBonus: damage[1][1],
                    damageType: includedDamageType[damageRolls],
                }

                damageRolls++;
                actionValues.damage.push(damageValue);

            } else if (key.startsWith('h')) {

            }
        }
    });

    if (rechargeOnly) {
        return actionValues.rechargeTurns;
    } else {
        return actionValues;
    }
}

function getSpecificDmgTypes(mainString, searchArray) {
    let results = [];

    searchArray.forEach(searchString => {
        let index = mainString.indexOf(searchString);
        if (index !== -1) {
            results.unshift(searchString);
        } else {

        }
    })

    return results;
}

function arrangeActions(actionArray) {
    actionArray.forEach(element => {

        let actionName = `${element.name}`
        actionName = actionName.replace(/\{@.*?\}/g, '')

        let actionRollValues = splitStringByPlaceholders(element.entries[0])
        actionRollValues.rechargeTurns = splitStringByPlaceholders(element.name, true)

        let actionDescription = `${element.entries[0]}`
        actionDescription = actionDescription.replaceAll("{@atk mw}", 'Melee,')
        actionDescription = actionDescription.replaceAll("{@atk rw}", 'Ranged,')
        actionDescription = actionDescription.replaceAll("{@atk mw,rw}", 'Melee/Ranged,')
        actionDescription = actionDescription.replaceAll("{@hit", '+')
        actionDescription = actionDescription.replaceAll("{@h", '')
        actionDescription = actionDescription.replaceAll("{@damage", '')
        actionDescription = actionDescription.replaceAll("{@dc", 'DC')
        actionDescription = actionDescription.replaceAll("{@condition", '')
        actionDescription = actionDescription.replaceAll("}", '')

        actions.value.push({
            type: 'row',
            rollValues: actionRollValues,
            name: `<strong>${actionName.trim()}:</strong> ${actionDescription.trim()}`,
        })
    });
}

if (props.monster.action !== undefined) {
    actions.value.push({
        type: 'title',
        name: 'ACTION',
    })
    arrangeActions(props.monster.action)
}
if (props.monster.bonus !== undefined) {
    actions.value.push({
        type: 'title',
        name: 'BONUS ACTION',
    })
    arrangeActions(props.monster.bonus)
}
if (props.monster.reaction !== undefined) {
    actions.value.push({
        type: 'title',
        name: 'REACTION',
    })
    arrangeActions(props.monster.reaction)
}
if (props.monster.legendary !== undefined) {
    actions.value.push({
        type: 'title',
        name: 'LEGENDARY ACTION',
    })
    arrangeActions(props.monster.legendary)
}



/* PASSIVE TAB */
let dmgResistance = []
let dmgImmunity = []
let conditionImmune = []
let vulnerable = []
let senses = []
let traits = []

if (props.monster.resist !== undefined) {
    dmgResistance = props.monster.resist;
    if (dmgResistance[0].resist !== undefined) {
        dmgResistance = `${dmgResistance[0].resist.join(', ')} ${dmgResistance[0].note}`
    }
}

if (props.monster.immune !== undefined) {
    dmgImmunity = props.monster.immune;
    if (dmgImmunity[0].immune !== undefined) {
        dmgImmunity = `${dmgImmunity[0].immune.join(', ')} ${dmgImmunity[0].note}`
    }
}

if (props.monster.conditionImmune !== undefined) {
    conditionImmune = props.monster.conditionImmune;
    if (conditionImmune[0].conditionImmune !== undefined) {
        conditionImmune = `${conditionImmune[0].conditionImmune.join(', ')} ${conditionImmune.note}`
    }
}

if (props.monster.vulnerable !== undefined) {
    vulnerable = props.monster.vulnerable;
    if (vulnerable[0].vulnerable !== undefined) {
        vulnerable = `${vulnerable[0].vulnerable.join(', ')} ${vulnerable.note}`
    }
}

if (props.monster.senses) {
    senses = props.monster.senses.join(', ')
}
traits = props.monster.trait


/* DMG BUTTON */

let dealingDmg = ref([
    {
        time: null,
        name: true,
    },
    {
        time: null,
        name: true,
    }
])

function calcDmg() {
    dealingDmg.value.forEach(dmgInstance => {
        let finalDmg = dmgInstance.time;

        if (typeof dmgResistance === 'string') {
            dmgResistance = dmgResistance.split(', ')
        }

        if (typeof dmgImmunity === 'string') {
            dmgImmunity = dmgImmunity.split(', ')
        }

        if (dmgResistance.some(type => type.includes(dmgInstance.name))) {
            finalDmg = finalDmg / 2
            console.log("dmg resistance: ", finalDmg, dmgInstance);
        }

        if (dmgImmunity.some(type => type.includes(dmgInstance.name))) {
            finalDmg = 0
            console.log("dmg immune: ", finalDmg, dmgInstance);
        }

        currentHp.value = currentHp.value - finalDmg
    })

}

</script>

<style lang="scss" scoped>
@import '../styles/variables.scss';

.monsterCard {
    max-width: 400px;
    max-height: 580px;
    width: 100%;
    height: 100%;

    background-color: $color-blue-25;
    border: 3px solid $color-blue-dark;
    border-radius: 25px;

    display: flex;
    flex-direction: column;
    overflow: hidden;

    .monsterTable {
        position: relative;
        height: 100%;
        width: 100%;
        display: flex;
        flex-direction: column;
        overflow-y: scroll;

        .monsterTableRow {
            width: 100%;
            height: 30px;
            padding: 0 5px;
            display: flex;
            align-items: center;
            gap: 10px;

            .monsterTableTime {
                display: inline-block;
                width: 24px;
                height: 24px;
                line-height: 24px;
                text-align: center;
                font-weight: bold;
                background-color: $color-blue-dark;
                color: #fff;
                border-radius: 5px;
            }

            .monsterTableName {
                display: inline-block;
                width: 100%;
            }

            .removeStatus {
                display: block;
                position: absolute;
                right: 10px;
                font-size: 20px;
                z-index: 3;
                pointer-events: none;
                opacity: 0;
                cursor: pointer;
            }

            &:hover .removeStatus {
                pointer-events: all;
                opacity: 1;
            }

            &:nth-child(odd) {
                background-color: $color-blue-75;
            }

            &:nth-child(even) {
                background-color: $color-blue-50;
            }

            &:last-child {
                border-bottom-left-radius: 10px;
                border-bottom-right-radius: 10px;
            }

            &:first-child {
                border-top-left-radius: 10px;
                border-top-right-radius: 10px;
            }

            &.addRow {
                background-color: $color-blue-highlight;
                color: #fff;
                width: 100%;
                justify-content: center;
                cursor: pointer;
                opacity: 0.5;
                transition: all ease-in-out 0.2s;

                &:hover {
                    opacity: 1;
                }
            }
        }
    }

    .monsterCardSettings {
        position: relative;
        padding: 20px;
        border-bottom: 3px solid $color-blue-dark;
        color: $color-blue-dark;

        .monsterCardControls {
            opacity: 0;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            transition: all ease-in-out 0.1s;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 20px;

            &:hover {
                opacity: 1;
                background-color: $color-blue-25;
            }

            >div {
                display: flex;
                flex-direction: row;

                span {
                    display: block;
                    padding: 8px 10px;
                    font-weight: bold;
                    font-size: 1.3rem;
                    cursor: pointer;
                }

                .controlDmg {
                    background-color: $color-red-100;
                    border-radius: 15px 0 0 15px;
                }

                .controlHeal {
                    background-color: $color-green-100;
                    border-radius: 0 15px 15px 0;
                }

                .controlEdit {
                    background-color: $color-blue-100;
                    border-radius: 15px 0 0 15px;
                }

                .controlCopy {
                    background-color: $color-blue-75;
                    border-radius: 0 15px 15px 0;
                }
            }
        }
    }

    .monsterTabSettings {
        display: flex;
        justify-content: space-around;
        gap: 5px;
        padding: 10px;
        border-bottom: 3px solid $color-blue-dark;
        color: $color-blue-dark;

        span {
            cursor: pointer;
            color: $color-blue-dark;
            font-size: 1.3rem;
            font-weight: bold;
            text-transform: uppercase;
            display: block;
            padding: 10px;
            border-radius: 15px;
            transition: all ease-in-out 0.2s;
            background-color: $color-blue-25;

            &.active {
                background-color: $color-blue-100;
            }
        }
    }

    .monsterMain {
        position: relative;
        overflow: hidden;
        padding: 20px;
        height: 100%;

        input {
            all: unset;
        }

        .monsterMainTab {
            display: flex;
            flex-direction: column;
            gap: 20px;
            height: 100%;
            width: 100%;



            &.hpTab {
                .monsterHpBar {
                    display: grid;
                    grid-template-columns: repeat(2, 1fr);
                    grid-template-rows: repeat(2, 1fr);
                    gap: 5px;

                    >div {
                        display: flex;
                        justify-content: space-between;
                        border-radius: 15px;
                        padding: 10px 8px;

                        >span:first-child {
                            font-weight: bold;
                            font-size: 1.3rem;
                        }

                        input {
                            width: 40px;
                            text-align: center;
                        }
                    }

                    .monsterSpeed {
                        background-color: $color-blue-100;
                        grid-row: span 2;
                        display: grid;
                        grid-template-columns: repeat(2, 1fr);
                        grid-template-rows: repeat(4, 1fr);

                        span:first-child {
                            grid-row: span 4;
                        }

                        p>span {
                            float: right;
                        }

                        input {
                            text-align: right;
                            float: right;
                            width: 35px;
                        }
                    }

                    .monsterHp {
                        background-color: $color-green-50;
                        font-size: 1.3rem;
                        line-height: 1.7rem;
                    }

                    .monsterAc {
                        background-color: $color-red-50;
                        font-size: 1.3rem;
                        line-height: 1.7rem;
                        input{
                            width: 100px;
                        }
                    }
                }


            }

            &.statsTab {
                .monsterStats {
                    display: grid;
                    grid-template-columns: repeat(2, minmax(0, 1fr));
                    grid-template-rows: repeat(3, minmax(0, 1fr));
                    gap: 10px;

                    .statBlock {
                        position: relative;
                        background-color: $color-blue-100;
                        border-radius: 15px;
                        padding: 5px 10px;
                        display: flex;
                        flex-direction: column;
                        justify-content: space-between;
                        font-size: 1.3rem;
                        gap: 0;
                        height: 65px;

                        >span:first-child {
                            display: block;
                            font-weight: bold;
                        }

                        input {
                            width: 30px;
                            text-align: center;
                        }

                        .doubleRoll {
                            font-size: 1.3rem;
                            font-weight: bold;
                            display: flex;
                            justify-content: center;
                            align-items: center;
                            height: 30px;
                            width: 50px;
                            border-radius: 10px;
                            z-index: 3;
                            position: absolute;
                            bottom: 5px;
                            right: 5px;
                            box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);

                            &.advantage {
                                background-color: $color-green-100;
                            }

                            &.disadvantage {
                                background-color: $color-red-100;
                            }
                        }
                    }
                }

                .monsterTable.skillTable .monsterTableRow .monsterTableTime {
                    width: 40px;

                    &.savingThrow {
                        background-color: $color-blue-highlight;
                    }
                }
            }

            &.actionsTab {
                .spellcastingOverlayButton{
                    display: inline-block;
                    width: auto;
                    height: 24px;
                    line-height: 24px;
                    text-align: center;
                    font-weight: bold;
                    background-color: $color-blue-dark;
                    color: #fff;
                    border-radius: 5px;
                    padding-left: 5px;
                    padding-right: 5px;
                }
                .monsterTable.actionTable {
                    .monsterTableRow {
                        height: auto;
                        padding: 5px;
                        font-size: .9rem;

                        .monsterTableTime {
                            width: 30px;

                            &.actionTitle {
                                background-color: $color-blue-highlight;
                                width: auto;
                                padding-left: 5px;
                                padding-right: 5px;
                            }
                        }

                    }
                }
            }

            &.passiveTab {
                gap: 0;
                overflow-y: scroll;

                .passiveTabWrapper {
                    display: flex;
                    flex-direction: column;
                    gap: 10px;

                    >div {
                        border-radius: 10px;
                        overflow: hidden;

                        >div {
                            padding: 5px;

                            &.titleGreen {
                                font-weight: bold;
                                font-size: 1.2rem;
                                background-color: $color-green-50;
                            }

                            &.listGreen {
                                background-color: $color-green-25;
                            }

                            &.titleRed {
                                font-weight: bold;
                                font-size: 1.2rem;
                                background-color: $color-red-50;
                            }

                            &.listRed {
                                background-color: $color-red-25;
                            }

                            &.titleSenses {
                                font-weight: bold;
                                font-size: 1.2rem;
                                background-color: $color-blue-100;
                            }

                            &.listSenses {
                                background-color: $color-blue-50;
                            }

                            &.titleAbility {
                                font-weight: bold;
                                font-size: 1.2rem;
                                background-color: $color-blue-75;
                            }

                            &.listAbility {
                                background-color: $color-blue-50;
                            }
                        }
                    }
                }
            }
        }
    }

    .monsterTable.dmgOverlay {
        input {
            all: unset;
        }

        .monsterTableRow .monsterTableTime {
            width: 60px;
        }

        .dealDmgButton {
            background-color: $color-red-100;
            font-size: 1.3rem;
            font-weight: bold;
            width: max-content;
            padding: 8px 10px;
            border-radius: 15px;
            margin-top: 30px;
            cursor: pointer;
        }
    }

    .monsterTable.availableSpells {
        .monsterTableRow {
            padding: 5px;
            height: auto;

            &:not(.titleRow) {
                text-transform: capitalize;
            }

            .spellGroupTitle {
                display: inline-block;
                width: auto;
                height: 24px;
                line-height: 24px;
                text-align: center;
                font-weight: bold;
                background-color: $color-blue-highlight;
                color: #fff;
                border-radius: 5px;
                padding-left: 5px;
                padding-right: 5px;
            }
        }
    }
}
</style>
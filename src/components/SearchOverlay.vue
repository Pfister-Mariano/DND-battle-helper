<template>
    <input class="searchInput" type="text" v-model="filterWord" @input="filterSearch()">

    <div class="searchWrapper">
        <table class="searchTable">
            <tr>
                <th></th>
                <th>Name</th>
                <th>Type</th>
                <th>Source</th>
                <th>CR</th>
                <th>HP</th>
            </tr>

            <tr v-for="(monster, index) in filteredMonsters" @click="addMonster(monster)">
                <td>{{ index + 1 }}</td>
                <td>{{ monster.name }}</td>
                <td>
                    {{ monster.searchDisplayType }}
                </td>
                <td>{{ monster.source }}</td>
                <td>{{ monster.searchDisplayCr }}</td>
                <td>{{ monster.searchDisplayHp }}</td>
            </tr>
        </table>
    </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';

let selectedMonster = defineModel()

let allMonsters = []
let filterWord = ref('')
let filteredMonsters = ref([]);

function filterSearch() {
    filteredMonsters = ref(allMonsters
        .map(monster => {
            if (monster.name.toLowerCase().includes(filterWord.value.toLowerCase())) {
                /* Type */
                if (typeof monster.type === 'object') {
                    let newMonsterType = `${monster.type.type} (${monster.type.tags})`
                    monster.searchDisplayType = newMonsterType
                } else {
                    monster.searchDisplayType = monster.type
                }

                /* CR */
                if (typeof monster.cr === 'object') {
                    let newMonsterCr = `Monster: ${monster.cr.cr}, Lair: ${monster.cr.lair}`
                    monster.searchDisplayCr = newMonsterCr
                } else {
                    monster.searchDisplayCr = monster.cr
                }

                /* HP */
                if (typeof monster.hp === 'object') {
                    let newMonsterHp = `${monster.hp.average} (${monster.hp.formula})`
                    monster.searchDisplayHp = newMonsterHp
                } else {
                    monster.searchDisplayHp = monster.hp
                }
                return monster;
            }
        })
        .filter(monster => monster !== undefined && monster !== null)
        .sort((a, b) => a.name.localeCompare(b.name))
    )
}

function addMonster(monster) {
    selectedMonster.value.push(monster)
}

onMounted(
    async () => {
        try {
            for (let i = 0; i <= 101; i++) {
                const response = await fetch('/data/' + i + '.json');
                const data = await response.json();

                let monsterData = data.monster

                monsterData.forEach((monster) => {
                    allMonsters.push(monster)
                })
            }
        } catch (error) {
            console.log(error);
        }
    }
)

</script>

<style lang="scss" scoped>
@import '../styles/variables.scss';

.searchInput {
    all: unset;
    background-color: $color-blue-dark;
    color: #fff;
    font-weight: bold;
    font-size: 1.3rem;
    letter-spacing: .05em;
    width: calc(100% - 100px);
    transform: translateY(-50%);
    padding: 10px 10px;
    border-radius: 15px;
}
.searchWrapper{
    overflow-y: scroll;
    height: calc(100% - 3rem);
}

.searchTable {
    width: 100%;
    text-align: center;
    border-collapse: collapse;

    tr:hover {
        height: max-content;
        background-color: $color-blue-50;
        cursor: pointer;
    }

    th,
    td {
        height: max-content;
        padding: 7px 0;
        border-bottom: 1px solid $color-blue-dark;
        transition: all ease-in-out 0.2s;
    }
}
</style>
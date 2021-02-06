<template>
  <div
    class="flex items-center justify-center space-x-3 mb-3 text-sm font-semibold uppercase w-full"
  >
    <div class="flex-auto flex space-x-3">
      <button
        class="w-32 h-10 flex items-center justify-center bg-black text-white"
        type="submit"
        @click="sort = 'name'"
      >
        Name
      </button>
      <button
        @click="sort = 'date'"
        class="w-32 flex items-center justify-center border border-gray-200"
        type="button"
      >
        Static Fire Date
      </button>
    </div>
  </div>

  <table class="min-w-full divide-y divide-gray-200">
    <thead class="bg-gray-50">
      <tr>
        <th class="px-6 py-3">Name</th>
        <th class="px-6 py-3">Static Fire Date</th>
        <th class="px-6 py-3">Image</th>
        <th class="px-6 py-3">Crews</th>
        <th class="px-6 py-3">Success</th>
        <th></th>
      </tr>
    </thead>
    <tbody
      v-for="(item, index) in sortData"
      :key="index"
      class="bg-white divide-y divide-gray-200"
    >
      <tr>
        <td>{{ item.name }}</td>
        <td>
          <span v-if="item.static_fire_date_utc">
            {{ moment(item.static_fire_date_utc).format("DD MMM YYYY") }}
            {{ moment(item.static_fire_date_utc).format("HH:mm") }}
          </span>
          <span v-else>-</span>
        </td>
        <td><img :src="item.links.patch.small" alt="" class="h-20 w-20" /></td>
        <td>{{ item.crew.length }}</td>
        <td>
          <span v-if="item.success" class="text-green-400">Release</span>
          <span v-else>Coming</span>
        </td>
        <td>
          <button
            @click="openModal(item)"
            type="button"
            class="bg-violet-100 text-violet-700 text-base font-semibold px-6 py-2 rounded-lg"
          >
            Detail
          </button>
        </td>
      </tr>
    </tbody>
  </table>

  <div v-if="visible" class="fixed z-10 inset-0 overflow-y-auto">
    <div
      class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0"
    >
      <div class="fixed inset-0 transition-opacity" aria-hidden="true">
        <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
      </div>

      <span
        class="hidden sm:inline-block sm:align-middle sm:h-screen"
        aria-hidden="true"
        >&#8203;</span
      >

      <div
        class="inline-block align-bottom bg-white rounded-lg text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full"
        role="dialog"
        aria-modal="true"
        aria-labelledby="modal-headline"
      >
        <div class="bg-white px-4 pt-5 pb-4 sm:p-4 sm:pb-4">
          <div class="flex items-center w-full justify-center  mb-5">
            <img :src="select.links.patch.small" alt="" class="h-40 w-40" />
          </div>

          <div class="mb-2">
            <div
              class="bg-green-100 text-green-700 px-4 py-1 rounded-lg "
              type="submit"
            >
              Name
            </div>
            <span>{{ select.name }}</span>
          </div>
          <div class="mb-2">
            <div
              class="bg-green-100 text-green-700 px-4 py-1 rounded-lg"
              type="submit"
            >
              Details
            </div>
            <span v-if="select.details">{{ select.details }}</span>
            <span v-else>-</span>
          </div>

          <div class="mb-2">
            <div
              class="bg-green-100 text-green-700 px-4 py-1 rounded-lg"
              type="submit"
            >
              Crews
            </div>
            <span>
              <template v-if="crews.length > 0">
                <div v-for="(item, index) in crews" :key="index">
                  <div>
                    <span>Name</span> <span>{{ item.name }}</span>
                  </div>
                </div>
              </template>
              <div v-else>-</div>
            </span>
          </div>

          <div class="mb-2">
            <div
              class="bg-green-100 text-green-700 px-4 py-1 rounded-lg"
              type="submit"
            >
              Rocket
            </div>
            <div>
              <span class="font-bold">Name : </span>
              <span>{{ rocket.name }}</span>
            </div>
            <div>
              <span class="font-bold">Company : </span>
              <span>{{ rocket.company }}</span>
            </div>
            <div>
              <span class="font-bold">Cost/Launch : </span>
              <span>{{ rocket.cost_per_launch }}</span>
            </div>
            <div>
              <span class="font-bold">Diameter : </span>
              <span>{{ rocket.diameter.meters }} meters</span>
            </div>
            <div>
              <span class="font-bold">Mass : </span>
              <span>{{ rocket.mass.kg }} kg</span>
            </div>
            <div>
              <span class="font-bold">Country : </span>
              <span>{{ rocket.country }}</span>
            </div>
            <div>
              <span class="font-bold">Description : </span>
              <span>{{ rocket.description }}</span>
            </div>
          </div>

          <div class="mb-2">
            <div
              class="bg-green-100 text-green-700 px-4 py-1 rounded-lg"
              type="submit"
            >
              Launchpad
            </div>
            <div>
              <span class="font-bold">Name : </span>
              <span>{{ launchpad.name }}</span>
            </div>
            <div>
              <span class="font-bold">Locality : </span>
              <span>{{ launchpad.locality }}</span>
            </div>
            <div>
              <span class="font-bold">Region : </span>
              <span>{{ launchpad.region }}</span>
            </div>
            <div>
              <span class="font-bold">Detail : </span>
              <span>{{ launchpad.details }}</span>
            </div>
          </div>
        </div>
        <div class="bg-gray-50 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
          <button
            @click="classModal"
            type="button"
            class="mt-3 w-full inline-flex justify-center rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white text-base font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm"
          >
            Cancel
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, computed } from "vue";
import axios from "axios";
import moment from "moment";
import _ from "lodash";

export default {
  name: "App",
  components: {},
  setup() {
    onMounted(async () => {
      let res = await axios.get("https://api.spacexdata.com/v4/launches");
      console.log("MOUNT", res.data);
      list.value = res.data;
    });

    const sortData = computed(() => {
      if (sort.value === "name") {
        return _.orderBy(list.value, "name", "asc");
      } else if (sort.value === "date") {
        return _.orderBy(list.value, "static_fire_date_utc", "asc");
      }
      return list.value;
    });

    const list = ref([]);
    const visible = ref(false);
    const select = ref(false);
    const crews = ref([]);
    const launchpad = ref(false);
    const rocket = ref(false);
    const sort = ref(false);

    const openModal = async (item) => {
      console.log("ITEM", item);

      visible.value = true;
      select.value = item;
      let resPromise = [];
      item.crew.forEach((item) => {
        resPromise.push(fetchMember(item));
      });
      let res = await Promise.all(resPromise);
      crews.value = res;
      let resLaunchpad = await fetchLaunchpad(item.launchpad);
      launchpad.value = resLaunchpad;
      let resRocket = await fetchRocket(item.rocket);
      rocket.value = resRocket;
    };

    const fetchMember = async (id) => {
      let res = await axios.get(`https://api.spacexdata.com/v4/crew/${id}`);
      return res.data;
    };

    const fetchLaunchpad = async (id) => {
      let res = await axios.get(
        `https://api.spacexdata.com/v4/launchpads/${id}`
      );
      return res.data;
    };

    const fetchRocket = async (id) => {
      let res = await axios.get(`https://api.spacexdata.com/v4/rockets/${id}`);
      return res.data;
    };

    const classModal = () => {
      visible.value = false;
    };

    return {
      openModal,
      classModal,
      list,
      moment,
      visible,
      select,
      crews,
      launchpad,
      rocket,
      sortData,
      sort,
    };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

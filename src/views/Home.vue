<template>
  <el-card shadow="always" class="main-card">
    <h1>{{ $t('h1') }}</h1>

    <section class="cards">
      <transition name="create" appear>
        <el-card
          key="create"
          shadow="hover"
          @click="$router.push('/kpis/create')"
          role="navigate-to-create"
        >
          <i class="el-icon-circle-plus"></i>
          <br /><br />
          <span>{{ $t('new-kpi') }}</span>
        </el-card>
      </transition>

      <transition name="kpis" appear>
        <el-card
          key="kpis"
          shadow="hover"
          @click="$router.push('/kpis')"
          role="navigate-to-actives"
        >
          <i class="el-icon-s-data"></i>
          <br /><br />
          <span>{{ $t('active-kpis') }}</span>
        </el-card>
      </transition>

      <transition name="deleted" appear>
        <el-card
          key="deletedKpis"
          shadow="hover"
          @click="$router.push('/kpis/deleted')"
          role="navigate-to-deleted"
        >
          <i class="el-icon-delete"></i>
          <br /><br />
          <span>{{ $t('deleted-kpis') }}</span>
        </el-card>
      </transition>

      <transition name="fields" appear>
        <el-card
          key="fields"
          shadow="hover"
          @click="$router.push('/fields')"
          role="navigate-to-fields"
        >
          <i class="el-icon-setting"></i>
          <br /><br />
          <span>{{ $t('dynamic-fields') }}</span>
        </el-card>
      </transition>
    </section>
  </el-card>
</template>

<style scoped>
h1 {
  font-size: 2rem;
}

.main-card .el-card {
  cursor: pointer;
  margin: 10px auto;
  width: 100%;
  background-color: #e7f6f6;
  border: 1px solid #99cccc;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
}

i {
  font-size: 5rem;
}

span {
  width: 100%;
}

.cards {
  max-width: 1200px;
  margin: 0 auto;
  display: grid;
  grid-gap: 1rem;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
}

/* Animations */
.create-enter-from,
.kpis-enter-from,
.deleted-enter-from,
.fields-enter-from {
  opacity: 0;
  transform: translateX(300px);
}

.create-enter-active {
  transition: all 0.2s ease-out;
}

.kpis-enter-active {
  transition: all 0.3s ease-out;
}

.deleted-enter-active {
  transition: all 0.4s ease-out;
}

.fields-enter-active {
  transition: all 0.5s ease-out;
}

.create-enter-to,
.kpis-enter-to,
.deleted-enter-to,
.fields-enter-to {
  opacity: 1;
  transform: translateX(0);
}
</style>

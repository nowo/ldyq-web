<script lang="ts" setup>
const props = defineProps<{
    type: 1 | 2 | 3 | 4 // 类型 1:普通新闻 2:客户案例 3:公司动态 4:公司公告
    goods?: boolean // 是否为商品数据
}>()

const route = useRoute()

const id = useRouteParam('id')

const url = props.goods ? '/api/page/product/detail' : '/api/page/news/detail'

const { data } = await useCustomFetch<Record<'nextNews' | 'prevNews' | 'data', INewsResponse>>(url, {
    params: {
        id: id.value,
        type: props.type,
    },
})
if (!data.value) {
    showErrorPage()
}

const news = data.value?.data
const nextNews = data.value?.nextNews
const prevNews = data.value?.prevNews

// 设置详情页链接地址
const setLinkPath = (row: INewsResponse) => {
    const path = route.matched[0].path.split('/:')[0]

    return `${path}/${row.id}`
}
// console.log(news)
</script>

<template>
    <div v-if="news"
        class="news-box">
        <h3 class="my15px text-center text-18px font-bold">
            {{ $lang(news?.title, news?.title_en) }}
        </h3>
        <div class="time text-center">
            <span class="mr10px">
                <!-- {{ $t('time') }} -->
                {{ $lang('发布时间', 'Release time') }}：{{ formatTime(new Date(news!.createdAt)) }}</span>
            <!-- <span>{{ $t('author') }}：{{ news?.author }}</span> -->
            <span>
                <!-- {{ $t('read') }} -->
                {{ $lang('浏览次数', 'Views') }}：{{ news?.read }}</span>
        </div>
        <!-- <div class="intro">
                {{ $lang(news?.describe, news?.describe_en) }}
            </div> -->
        <div class="cont"
            v-html="$lang(news?.content, news?.content_en)" />

        <div class="next-prev">
            <NuxtLinkLocale v-if="prevNews?.id"
                :to="setLinkPath(prevNews)">
                <!-- {{ $t('prev') }} -->
                {{ $lang('上一个', 'Prev') }}： {{ $lang(prevNews?.title, prevNews?.title_en) }}
            </NuxtLinkLocale>
            <span v-else>
                <!-- {{ $t('prev') }}：{{ $t('none') }} -->
                {{ $lang('上一个', 'Prev') }}：{{ $lang('无', 'none') }}
            </span>
            <NuxtLinkLocale v-if="nextNews?.id"
                :to="setLinkPath(nextNews)">
                <!-- {{ $t('next') }} -->
                {{ $lang('下一个', 'Next') }}： {{ $lang(nextNews?.title, nextNews?.title_en) }}
            </NuxtLinkLocale>
            <span v-else>
                <!-- {{ $t('next') }}：{{ $t('none') }}  -->
                {{ $lang('下一个', 'Next') }}：{{ $lang('无', 'none') }}
            </span>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.news-box {
    .time {
        color: var(--el-color-info);
        background-color: var(--el-color-info-light-9);
        padding: 5px;
        font-size: 14px;
        margin-bottom: 20px;
    }

    .next-prev {
        border-top: 1px solid var(--el-color-info-light-8);
        padding: 10px 0;
        margin-top: 30px;
        display: flex;
        justify-content: space-between;
        font-size: 14px;
        color: var(--el-color-info);

        a:hover {
            color: var(--co-main-color);
        }
    }
}
</style>

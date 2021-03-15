<template>
    <div>
        <div class="mb-2">
            <small><b>{{ message.from.name }}</b> {{ message.created_at }}</small>
        </div>
        <div>
            {{ message.text }}
        </div>
        <br>
    </div>
</template>

<script>
export default {
    props: {
        message: {
            type: Object,
            required: true
        }
    }
}
</script>

<style>

</style>

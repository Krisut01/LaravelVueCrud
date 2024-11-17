<template>
    <div class="min-h-screen bg-gray-100 py-10">
        <div class="container mx-auto px-4">
            <h1 class="text-4xl font-bold text-center text-gray-800 mb-8">Posts</h1>

            <!-- Form Section -->
            <div class="bg-white rounded-lg shadow-lg p-6 mb-8">
                <form @submit.prevent="handleSubmit" class="space-y-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Title</label>
                        <input
                            v-model="formData.title"
                            type="text"
                            placeholder="Enter post title"
                            class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring-blue-500 focus:border-blue-500"
                        />
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Content</label>
                        <textarea
                            v-model="formData.content"
                            placeholder="Enter post content"
                            rows="4"
                            class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:ring-blue-500 focus:border-blue-500"
                        ></textarea>
                    </div>
                    <button
                        type="submit"
                        class="w-full bg-blue-500 text-white font-medium py-2 px-4 rounded-md hover:bg-blue-600"
                    >
                        {{ isEditing ? 'Update Post' : 'Create Post' }}
                    </button>
                    <button
                        v-if="isEditing"
                        @click="cancelEdit"
                        type="button"
                        class="w-full mt-2 bg-gray-500 text-white font-medium py-2 px-4 rounded-md hover:bg-gray-600"
                    >
                        Cancel Edit
                    </button>
                </form>
            </div>

            <!-- Posts Section -->
            <div class="space-y-4">
                <div
                    v-for="post in posts"
                    :key="post.id"
                    class="bg-white rounded-lg shadow-lg p-6"
                >
                    <h2 class="text-2xl font-bold text-gray-800 mb-2">{{ post.title }}</h2>
                    <p class="text-gray-700 mb-4">{{ post.content }}</p>
                    <div class="flex space-x-4">
                        <button
                            @click="startEdit(post)"
                            class="bg-yellow-500 text-white font-medium py-2 px-4 rounded-md hover:bg-yellow-600"
                        >
                            Edit
                        </button>
                        <button
                            @click="deletePost(post.id)"
                            class="bg-red-500 text-white font-medium py-2 px-4 rounded-md hover:bg-red-600"
                        >
                            Delete
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";

export default {
    data() {
        return {
            posts: [],
            formData: { title: "", content: "" },
            isEditing: false,
            editingPostId: null,
        };
    },
    methods: {
        async fetchPosts() {
            const response = await axios.get("/api/posts");
            this.posts = response.data;
        },
        async handleSubmit() {
            if (this.isEditing) {
                // Update Post
                await axios.put(`/api/posts/${this.editingPostId}`, this.formData);
                const postIndex = this.posts.findIndex((post) => post.id === this.editingPostId);
                this.posts[postIndex] = { ...this.formData, id: this.editingPostId };
                this.resetForm();
            } else {
                // Create Post
                const response = await axios.post("/api/posts", this.formData);
                this.posts.push(response.data);
                this.resetForm();
            }
        },
        startEdit(post) {
            this.formData = { title: post.title, content: post.content };
            this.isEditing = true;
            this.editingPostId = post.id;
        },
        cancelEdit() {
            this.resetForm();
        },
        resetForm() {
            this.formData = { title: "", content: "" };
            this.isEditing = false;
            this.editingPostId = null;
        },
        async deletePost(id) {
            await axios.delete(`/api/posts/${id}`);
            this.posts = this.posts.filter((post) => post.id !== id);
        },
    },
    mounted() {
        this.fetchPosts();
    },
};
</script>

<style>
/* Optional: Add global styles or Tailwind customization in your configuration. */
</style>

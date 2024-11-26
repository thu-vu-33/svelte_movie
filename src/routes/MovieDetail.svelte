<script lang="ts">
  import { onMount } from 'svelte';
  import { navigate } from 'svelte-routing';
  import { actionMovies, recentMovies } from '../data/movies';
  import VideoPlayer from '../components/VideoPlayer.svelte';
  import ErrorMessage from '../components/ErrorMessage.svelte';
  import LoadingState from '../components/LoadingState.svelte';
  import type { Movie } from '../types/movie';

  export let id: string;
  let isPlaying = false;
  let movie: Movie | null = null;
  let error: string | null = null;
  let isLoading = true;

  onMount(async () => {
    try {
      const movieId = parseInt(id);
      const foundMovie = [...actionMovies, ...recentMovies].find(m => m.id === movieId);
      
      if (!foundMovie) {
        error = "Movie not found";
      } else {
        movie = foundMovie;
      }
    } catch (e) {
      error = "Failed to load movie details";
    } finally {
      isLoading = false;
    }
  });

  function togglePlay() {
    if (!movie?.videoUrl) {
      error = "Video URL not available";
      return;
    }
    isPlaying = !isPlaying;
  }
</script>

{#if isLoading}
  <LoadingState />
{:else if error}
  <ErrorMessage message={error} />
{:else if movie}
  <div class="py-8">
    <div class="bg-white rounded-lg shadow-lg overflow-hidden">
      <div class="relative">
        {#if !isPlaying}
          <img src={movie.image} alt={movie.title} class="w-full h-[450px] object-cover">
          <button
            on:click={togglePlay}
            class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 bg-red-600 text-white rounded-full p-6 hover:bg-red-700 transition-colors"
          >
            <svg class="w-12 h-12" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M14.752 11.168l-3.197-2.132A1 1 0 0010 9.87v4.263a1 1 0 001.555.832l3.197-2.132a1 1 0 000-1.664z" />
            </svg>
          </button>
        {:else}
          <VideoPlayer 
            title={movie.title} 
            videoUrl={movie.videoUrl} 
            onStop={togglePlay} 
          />
        {/if}
      </div>

      <div class="p-6">
        <h1 class="text-3xl font-bold mb-4">{movie.title}</h1>
        <div class="flex flex-wrap gap-4 text-gray-600 mb-4">
          <span class="flex items-center">
            <svg class="w-5 h-5 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
            </svg>
            {movie.year}
          </span>
          {#if movie.duration}
            <span class="flex items-center">
              <svg class="w-5 h-5 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
              {movie.duration}
            </span>
          {/if}
          {#if movie.rating}
            <span class="text-yellow-500 flex items-center">
              <svg class="w-5 h-5 mr-1" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              {movie.rating}
            </span>
          {/if}
        </div>
        {#if movie.description}
          <p class="text-gray-700 leading-relaxed">{movie.description}</p>
        {/if}
      </div>
    </div>
  </div>
{/if}
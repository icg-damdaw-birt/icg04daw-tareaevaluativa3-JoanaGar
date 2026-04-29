<script lang="ts">
  import type { Movie } from '$lib/types';

  // Props con Svelte 5: sistema de tipos explícito y callbacks en lugar de eventos
  let { 
    movie,
    showActions = true,
    ondelete,
    onedit,
    onfavorite,
    onrate
  }: {
    movie: Movie;
    showActions?: boolean;
    ondelete?: (id: string) => void;
    onedit?: (movie: Movie) => void;
    onfavorite?: (id: string) => void;
    onrate?: (movie: Movie, rating: number) => void;
  } = $props();

  // Handlers: ejecutan callbacks del padre directamente
  function handleDelete() {
    ondelete?.(movie.id);
  }

  function handleEdit() {
    onedit?.(movie);
  }

  function handleFavorite() {
    onfavorite?.(movie.id);
  }

  function handleRate(rating: number) {
    onrate?.(movie, rating);
  }
</script>

<!-- Componente reutilizable: tarjeta para mostrar información de una película -->
<article class="flex flex-col overflow-hidden rounded-lg border border-slate-200 bg-white shadow-sm">
  {#if movie.posterUrl}
    <div class="flex h-48 items-center justify-center bg-slate-100">
      <img
        alt={`Póster de ${movie.title}`}
        class="max-h-full max-w-full object-contain"
        src={movie.posterUrl}
        loading="lazy"
      />
    </div>
  {/if}

  <div class="flex flex-1 flex-col gap-3 p-4">
    <header>
      <h3 class="text-lg font-semibold text-slate-900">{movie.title}</h3>
      <p class="text-sm text-slate-600">Dirigida por {movie.director}</p>
    </header>

    <div class="mt-auto text-sm text-slate-500">
      {#if movie.year}
        <span>Año: {movie.year}</span>
      {/if}

      <div class="flex items-center gap-1 mt-2">
        {#each Array(5) as _, i}
          {@const starValue = i + 1}
          <span
            class="cursor-pointer text-xl {movie.rating && movie.rating >= starValue ? 'text-yellow-400' : 'text-gray-300'}"
            onclick={() => handleRate(starValue)}
            onmouseenter={(e) => e.currentTarget.classList.add('text-yellow-300')}
            onmouseleave={(e) => e.currentTarget.classList.remove('text-yellow-300')}
          >
            {movie.rating && movie.rating >= starValue ? '⭐' : '☆'}
          </span>
        {/each}
        {#if movie.rating && movie.rating > 0}
          <span class="ml-2 text-xs text-slate-600">({movie.rating}/5)</span>
        {/if}
      </div>
    </div>

    {#if showActions}
      <div class="mt-3 flex flex-col gap-2 sm:flex-row">
        <button
          type="button"
          class="w-full rounded border px-3 py-2 transition {movie.isFavorite 
            ? 'border-red-500 bg-red-50 text-red-600 hover:bg-red-100' 
            : 'border-slate-300 text-slate-700 hover:bg-slate-50'}"
          onclick={handleFavorite}
          title={movie.isFavorite ? 'Quitar de favoritos' : 'Añadir a favoritos'}
        >
          {movie.isFavorite ? '❤️' : '🤍'} Favorito
        </button>
        <button
          type="button"
          class="w-full rounded border border-slate-300 px-3 py-2 text-slate-700 transition hover:bg-slate-50"
          onclick={handleEdit}
        >
          Editar
        </button>
        <button
          type="button"
          class="w-full rounded border border-red-500 px-3 py-2 text-red-600 transition hover:bg-red-50"
          onclick={handleDelete}
        >
          Eliminar
        </button>
      </div>
    {/if}
  </div>
</article>
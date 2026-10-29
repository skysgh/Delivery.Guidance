# Named Data Access

Use this convention whenever client or presentation code needs to retrieve, show, choose, create, change or delete
data. It applies to collections, single records, list choices and graph or tree navigation alike.

## Rule

**All data retrieval and saving, local or remote, goes by a registered key through one broker.** A presentation
component never knows an address, a transport or an implementation class. It knows a name.

```text
Presenter shows items, each knowing  { type key, record id }
  -> registry resolves the type key to a registration
       -> the registration names: a broker, a Player for reading, optionally a Player and form for writing
  -> broker loads the record by id          -> the Player shows it
  -> the Player emits a save intent         -> the broker saves it
Collections: the same broker answers a query (search, filters, sort, page) with rows
```

## One boundary, one registry

- The **broker** is the single boundary for listing, loading, saving, deleting and named actions for one kind of
  record. It owns the mapping to the real endpoint or store and it enforces authorisation. The server remains the
  authority; the client broker only calls it.
- There is **one registry** of brokers, keyed by name. Two registries answering "give me data for this name" is a
  defect: they drift, disagree about save and authorisation, and force every consumer to know which to ask.
- A source is identified by **key only**. A caller does not know whether the key is a local in-memory list or a
  remote store. A published definition may name a key, never an address and never a component class.

## Who does what

| Role | Owns | Never |
| --- | --- | --- |
| Presenter (browse, graph, picker panel) | How a collection is shown and explored; emits the type key and id of the chosen item | Fetches, saves, authorises |
| Player | One record's read, edit or add lifecycle, and the save intent | Owns collection state; posts to an endpoint |
| Player selection | Chooses the Player from the registration for the type key, not from the data's shape | Is a switch inside a template |
| Broker | List, load, save, delete, named actions, authorisation, endpoint mapping | Knows any component |
| Source answering a query | Search, filtering, ordering and paging, in memory if local and on the server if remote | Leaves them to the caller to re-apply to a returned page |

## Local and remote are the same call

A small static list (a closed vocabulary) is registered as a **read-only broker**: it can list and load and it has
no write. A server-backed list is a broker that calls the server. The caller asks the same question of both:
the same query (search term, page, page size, filters) and the same answer shape. Search and paging therefore reach
whichever source answers; a local source searches memory and a remote one pushes the search to the server.

Resolving a stored identifier to a title is a **load by id**, never a fetch of the whole list. A source that cannot
resolve an id reports it as not found; the caller shows the stored value flagged rather than dropping it.

## Replacing a parallel path (standing protocol)

When an older, narrower mechanism (for example a read-only fetch-by-key registry) exists beside the broker registry, replace
it in this order, and finish in the same change set rather than leaving the old path "for later":

1. **Mark the old path obsolete**: a `@deprecated` note naming the replacement, and no new consumers or features.
2. **Prove the new path.** Tests that fail if it breaks, covering what the old path did (including every rule worth
   keeping, for example: a published key may never be an address; an unknown key fails loudly) and what it could not do.
   Where callers must not change while sources move, put a single by-name read seam in front of both for the interim.
3. **Migrate every consumer**, specs included, least risky first, recording each step.
4. **Delete the old implementation**, and every comment, instruction, skill and document that describes it. A search for its
   name must find nothing except dated history.

A deprecated class whose last consumer has moved is detritus: it misleads the next person or agent about which path is real.
If a consumer genuinely cannot move yet, the old path stays marked obsolete and the blocker is recorded with an owner.

## Review checks

- No component, Presenter, Player or picker calls an HTTP client or an endpoint directly.
- Every list and every save names a registered key; no address appears in a published definition.
- A save goes through the broker only.
- A new source works as a broker (read-only for a local list) rather than as a new kind of registry.
- A replaced mechanism is deleted with its notes, not left deprecated after its last consumer has moved.
- Search and paging are executed by the source, and a test shows they reach it.
- Where an older parallel mechanism is still used, the report names it and the step that removes it.

[Up: Development Conventions](./readme.md)

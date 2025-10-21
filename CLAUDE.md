# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the companion code repository for the book **"Grokking Functional Programming"** by Michał Płachta. It's optimized as a learning tool, not a production codebase. The code demonstrates functional programming concepts progressively, chapter by chapter.

### Key Characteristics

- **Dual Language Structure**: Imperative/OO examples are in Java (`src/main/java`), functional examples are in Scala (`src/main/scala`)
- **Simplified Scala Subset**: Uses only FP-universal features introduced progressively through the book (see `chA_ScalaCheatSheet.scala`)
- **Learning-Optimized**: Intentionally inconsistent across chapters to demonstrate progressive concept introduction
- **Chapter-Based Organization**: Files are prefixed with chapter numbers (e.g., `ch01_`, `ch08_`, `ch12_`)

## Build & Development Commands

### Essential Commands

```bash
# Start interactive REPL (primary learning workflow)
sbt console

# Run all examples from all chapters
sbt runAll

# Run a specific chapter's main
sbt "runMain ch08_CastingDie"

# Run tests (primarily Chapter 12)
sbt test

# Run specific test suite
sbt "testOnly ch12_TravelGuideTest"

# Interactive run selection
sbt run
```

### REPL Workflow

The `sbt console` is the primary way to work with the book:

```scala
// Import existing chapter code
:load src/main/scala/ch01_IntroScala.scala

// Import specific modules
import ch09_CurrencyExchange.model._

// Reset REPL between chapters (recommended)
:reset
```

The REPL comes pre-configured with automatic imports (defined in `build.sbt`):
- `fs2._`, `cats.effect._`, `cats.implicits._`
- `scala.concurrent.duration._`, `java.util.concurrent._`
- Apache Jena query imports for Wikidata examples

## Architecture & Code Organization

### Progressive Learning Structure

**Early Chapters (1-4)**: Basic FP concepts
- Pure functions, immutability
- Higher-order functions
- Lists and transformations

**Mid Chapters (5-7)**: Advanced transformations
- `flatMap`, for-comprehensions
- Options, nested transformations
- Filter, map, fold patterns

**Late Chapters (8-12)**: Real-world FP with cats-effect
- `IO` type for side effects
- `Stream` for infinite/lazy sequences
- Concurrent programming with cats-effect
- Testing functional programs
- Real API integration (Wikidata via Apache Jena)

### Key Dependencies

- **cats-effect 3.6.0** (ch8-12): IO monad for effect management
- **fs2 3.11.0** (ch9-10): Functional streams
- **scalatest 3.2.19** (ch12): Testing framework
- **Apache Jena 5.3.0** (ch11-12): SPARQL/Wikidata queries
- **Akka 2.6.20**: Imperative comparison examples

### Important Patterns

**IO-based Effectful Code** (ch8+):
```scala
// Wrapping unsafe operations
def castTheDie(): IO[Int] = IO.delay(unsafeOperation())

// Combining IOs with for-comprehension
for {
  a <- operation1()
  b <- operation2(a)
} yield result
```

**Retry Pattern** (ch8):
```scala
retry(operation: IO[A], maxRetries: Int): IO[A]
// Used throughout later chapters for API reliability
```

**Stream Processing** (ch9-10):
```scala
Stream.eval(io)              // Lift IO into Stream
  .repeat                    // Infinite repetition
  .take(n)                   // Limit results
  .compile.toList           // Execute and collect
```

**Data Access Pattern** (ch11-12):
```scala
trait DataAccess {
  def findAttractions(...): IO[List[Attraction]]
  def findArtistsFromLocation(...): IO[List[Artist]]
}
// Pure interface, implementation separated
```

## Testing (Chapter 12)

Test files: `src/test/scala/ch12_TravelGuideTest.scala`, `src/test/scala/ch12_BasicTest.scala`

**Testing Approach**:
- Property-based testing with ScalaCheck
- Example-based testing with ScalaTest
- Testing IO-based code with `unsafeRunSync()`
- Local Fuseki server for integration tests

## Common Development Tasks

### Adding New Examples

Follow chapter naming convention:
- `chXX_FeatureName.scala` for Scala examples
- `chXX_FeatureName.java` for Java comparisons
- Add `main` method for standalone execution
- Update `runAll` alias in `build.sbt` if needed

### Working with External APIs

**Wikidata Integration** (ch11-12):
- Uses Apache Jena for SPARQL queries
- Connection management with `Resource[IO, RDFConnection]`
- API calls wrapped in `IO.delay()` for safety
- Retry logic applied for reliability

### Dev Container Support

A complete Dev Container configuration is available (`.devcontainer/`):
- Scala 3.6.4, sbt 1.10.11, JDK 21
- VS Code Metals integration
- Persistent dependency caches
- See `README-DEVCONTAINER.md` for setup (in Japanese)

## Important Constraints

1. **Simplified Scala**: Not idiomatic production Scala, intentionally simplified for learning
2. **Plain `assert`**: Most chapters use basic assertions; proper testing only in ch12
3. **Progressive Features**: Earlier chapters avoid features not yet introduced
4. **Mixed Styles**: Intentional inconsistency for pedagogical purposes

## File-Specific Notes

- **`chA_ScalaCheatSheet.scala`**: Reference for all Scala features used in the book
- **`ch08_SchedulingMeetings.scala`**: Contains the `retry` function used throughout later chapters
- **`ch09_CurrencyExchange.scala`**: Complex example showing progression from v1-v4 implementations
- **`ch11_WikidataDataAccess.scala`**: Wikidata integration utilities
- **`ch12_TravelGuide.scala`**: Final application bringing together all concepts

## External Resources

- Book webpage: https://michalplachta.com/book
- For issues/updates, check the book's webpage first
- Wikidata SPARQL endpoint may change; check for repository updates

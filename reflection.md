# Profile Comparison Reflections

This file compares the outputs of different user profiles run through VibeFinder 1.0 and explains why the results differ.

---

## Happy Pop Listener vs. Chill Lofi Student

The Happy Pop Listener (genre=pop, mood=happy, energy=0.8) got upbeat, high-energy tracks at the top — *Sunrise City* scored 5.18 with genre + mood + energy all aligning. The Chill Lofi Student (genre=lofi, mood=chill, energy=0.35) got the complete opposite: *Library Rain* scored 5.29 with a perfect energy match and acoustic bonus. These two profiles are polar opposites in energy and genre, and the output reflects that clearly. The system successfully separates "pump-up pop" from "study lofi" because all three of their scoring dimensions point in different directions.

---

## High-Energy Rock Fan vs. Adversarial Metal Fan

The Rock Fan (genre=rock, mood=intense, energy=0.9) got both Voltline tracks at the top (~5.09) because genre match added +2.0 on top of strong mood and energy scores. The Metal Fan (genre=metal, mood=intense, energy=0.95) got zero genre matches since there is no metal in the catalog — so the system fell back entirely on mood and energy, surfacing EDM and rock instead. The top score dropped from ~5.09 to ~3.20. This shows how much the +2.0 genre weight matters: without it, the system can still find reasonable results, but the confidence (score) drops significantly. The Metal Fan results are not wrong, they are just the best available given the catalog gap.

---

## Happy Pop Listener vs. High-Energy Rock Fan

Both profiles want high energy (0.8 vs. 0.9) and non-acoustic music, but their genre and mood differ (pop/happy vs. rock/intense). The Pop Listener's top 5 is dominated by pop and mood=happy tracks. The Rock Fan's top 5 is dominated by rock and mood=intense tracks. *Gym Hero* (pop, intense, energy=0.93) appears in both lists because it sits in between — high energy and intense mood earn it points for the rock profile even without a genre match. This overlap shows that energy is a cross-genre bridge: a very high-energy pop song can sneak into a rock fan's recommendations when there are not enough rock songs to fill all 5 slots.

---

## Edge Case (High-Energy + Sad) vs. Chill Lofi Student

The Edge Case profile (genre=indie pop, mood=sad, energy=0.9) is unusual — it wants sad mood but high energy, which rarely co-exist in the catalog. *Rooftop Lights* ranked #1 at 4.00 despite being happy, not sad, because genre match (+2.0) and energy similarity dominated the score. *Autumn Letters* matched the mood but its low energy (0.38) lost too many energy-similarity points to overtake. The Chill Lofi Student had no such conflict — all its preferences pointed in the same direction — and scored higher overall (5.29 vs. 4.00). The takeaway: when a user's preferences conflict with each other, the system resolves it through arithmetic, and whichever feature has the higher weight wins. That is not always what the listener actually wants.

---

## Adversarial Metal Fan vs. Edge Case (High-Energy + Sad)

Both profiles failed to get a genre match — metal does not exist in the catalog, and indie pop has only two songs. But the Metal Fan's fallback was smoother: mood=intense and energy=0.95 are well-represented, so the top 5 felt coherent (all intense, high-energy tracks). The Edge Case profile had a harder time because mood=sad with energy=0.9 is an unusual combination — most sad songs in the catalog are also low-energy. The results ended up being genre-driven rather than mood-driven, which probably does not match what the user wanted. This comparison highlights that the system handles missing genres better than it handles contradictory feature combinations.

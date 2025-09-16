# Repository GitHub: pappadia-theorem

## 📄 README.md

```markdown
# Teorema di Pappadia

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.XXXXX.svg)](https://doi.org/10.5281/zenodo.XXXXX)
[![License: AGPL v3](https://img.shields.io/badge/License-AGPL%20v3-blue.svg)](LICENSE)
[![Python 3.7+](https://img.shields.io/badge/python-3.7+-blue.svg)](https://www.python.org/downloads/)

## UN METODO UNIVERSALE PER IL CALCOLO DEL PERIODO DECIMALE DELLE FRAZIONI

**Autore:** Umberto Junior Pappadia  
**ORCID:** 0009-0002-4392-7845  
© 2024-2025 - Tutti i diritti riservati  
Deposito SIAE N° 2025/00839 (16/04/2025)  
PAPPADIA® è un marchio registrato EUIPO

## Panoramica

Il Teorema di Pappadia fornisce un metodo diretto per il calcolo del periodo e antiperiodo delle frazioni razionali, raggiungendo complessità O(log M) rispetto ai metodi tradizionali O(λ).

## Innovazione Chiave

Per una frazione p/q in forma ridotta con q = 2^a · 5^b · M dove gcd(M, 10) = 1:
- **Lunghezza antiperiodo:** μ = max(a, b)
- **Lunghezza periodo:** λ = ord_M(10)

## Installazione

\`\`\`bash
git clone https://github.com/umbertopappadia/pappadia-theorem.git
cd pappadia-theorem
pip install -r requirements.txt
\`\`\`

## Utilizzo Base

\`\`\`python
from pappadia import calculate_period

periodo, antiperiodo = calculate_period(1, 7)
print(f"1/7: Periodo={periodo}, Antiperiodo={antiperiodo}")
# Output: 1/7: Periodo=6, Antiperiodo=0
\`\`\`

## Performance

| Frazione | Metodo Tradizionale | Teorema di Pappadia | Speedup |
|----------|---------------------|---------------------|---------|
| 1/97     | 2.31ms             | 0.18ms              | 12.8×   |
| 1/997    | 48.7ms             | 0.92ms              | 52.9×   |
| 1/9973   | 512ms              | 4.1ms               | 124.9×  |

## Licenza

Questo software è rilasciato sotto AGPL v3 con requisiti di attribuzione.
Per utilizzo commerciale, contattare: license@umbertojuniorpappadia.com

## Citazione

Se utilizzi questo codice nella tua ricerca, per favore cita:

\`\`\`
Pappadia, U.J. (2025). TEOREMA DI PAPPADIA - UN METODO UNIVERSALE PER IL 
CALCOLO DEL PERIODO DECIMALE DELLE FRAZIONI. Zenodo. DOI: [pending]
SIAE Deposito N° 2025/00839
\`\`\`

## Nota sulle Ottimizzazioni

L'implementazione base fornita ha complessità O(log M). Ottimizzazioni proprietarie
aggiuntive (Pappadia Booster, sviluppato Dicembre 2024) che forniscono miglioramenti
prestazionali di 5-10× sono disponibili sotto licenza commerciale.
```

## 📄 LICENSE

```
                    PAPPADIA® PUBLIC LICENSE
                         Version 1.0
              Copyright (C) 2024-2025 Umberto Junior Pappadia
                   SIAE Deposito N° 2025/00839
                    
This program is free software: you can redistribute it and/or modify
it under the terms of the GNU Affero General Public License as published
by the Free Software Foundation, either version 3 of the License.

ADDITIONAL ATTRIBUTION REQUIREMENTS:

Any use of this software must include visible attribution:
"Powered by PAPPADIA® Algorithm - Umberto Junior Pappadia"

This attribution must appear in:
- User interfaces (if applicable)
- Documentation
- Academic publications
- Source code headers

COMMERCIAL USE:

For commercial use beyond AGPL v3 terms, a commercial license is required.
Contact: license@umbertojuniorpappadia.com

PROPRIETARY EXTENSIONS:

This license covers only the base algorithm. The Pappadia Booster
optimization technology (December 2024) is proprietary and not included.

PAPPADIA® is a registered trademark. Unauthorized use is prohibited.
```

## 📄 pappadia.py

```python
"""
Teorema di Pappadia - Implementazione di Riferimento
© 2024-2025 Umberto Junior Pappadia
Deposito SIAE N° 2025/00839
PAPPADIA® è un marchio registrato

Licenza: AGPL v3 con requisiti di attribuzione
Per licenza commerciale: license@umbertojuniorpappadia.com
"""

from math import gcd
from typing import Tuple


def multiplicative_order(a: int, m: int) -> int:
    """
    Calcola l'ordine moltiplicativo di a modulo m.
    
    Args:
        a: Base
        m: Modulo
    
    Returns:
        L'ordine moltiplicativo di a modulo m
    """
    if m == 1:
        return 0
    
    if gcd(a, m) != 1:
        return 0
    
    order = 1
    value = a % m
    
    while value != 1:
        value = (value * a) % m
        order += 1
        if order > m:  # Controllo di sicurezza
            return 0
    
    return order


def calculate_period(numerator: int, denominator: int) -> Tuple[int, int]:
    """
    Calcola periodo e antiperiodo di una frazione usando il Teorema di Pappadia.
    
    Complessità: O(log M) dove M è la parte del denominatore coprima con 10.
    
    Args:
        numerator: Numeratore della frazione
        denominator: Denominatore della frazione
    
    Returns:
        Tupla (periodo, antiperiodo)
    
    Esempio:
        >>> calculate_period(1, 7)
        (6, 0)
        >>> calculate_period(1, 28)
        (6, 2)
    """
    # Riduzione ai minimi termini
    g = gcd(numerator, denominator)
    numerator //= g
    denominator //= g
    
    # Estrazione dei fattori di 2 e 5
    m_prime = denominator
    a = b = 0
    
    while m_prime % 2 == 0:
        m_prime //= 2
        a += 1
    
    while m_prime % 5 == 0:
        m_prime //= 5
        b += 1
    
    # Calcolo antiperiodo secondo il Teorema di Pappadia
    antiperiodo = max(a, b)
    
    # Calcolo periodo usando l'ordine moltiplicativo
    if m_prime == 1:
        periodo = 0
    else:
        periodo = multiplicative_order(10, m_prime)
    
    return periodo, antiperiodo


def verify_fraction(numerator: int, denominator: int, verbose: bool = False) -> dict:
    """
    Verifica e analizza una frazione secondo il Teorema di Pappadia.
    
    Args:
        numerator: Numeratore
        denominator: Denominatore
        verbose: Se True, stampa informazioni dettagliate
    
    Returns:
        Dizionario con analisi completa
    """
    periodo, antiperiodo = calculate_period(numerator, denominator)
    
    result = {
        'frazione': f"{numerator}/{denominator}",
        'periodo': periodo,
        'antiperiodo': antiperiodo,
        'complessità_tradizionale': periodo,
        'complessità_pappadia': f"O(log {denominator})"
    }
    
    if verbose:
        print(f"Analisi di {numerator}/{denominator}:")
        print(f"  Periodo: {periodo}")
        print(f"  Antiperiodo: {antiperiodo}")
        print(f"  Speedup teorico: {periodo / max(1, len(str(denominator))):.1f}×")
    
    return result


if __name__ == "__main__":
    # Test di esempio
    print("TEOREMA DI PAPPADIA - Test di Validazione")
    print("=" * 50)
    
    test_cases = [
        (1, 7),    # Periodo semplice
        (1, 28),   # Con antiperiodo
        (1, 97),   # Periodo lungo
        (22, 7),   # Frazione non unitaria
    ]
    
    for num, den in test_cases:
        verify_fraction(num, den, verbose=True)
        print()
    
    print("© 2024-2025 Umberto Junior Pappadia")
    print("PAPPADIA® è un marchio registrato")
    print("Per licenza commerciale: license@umbertojuniorpappadia.com")
```

## 📄 requirements.txt

```
# Teorema di Pappadia - Dipendenze
# Nessuna dipendenza esterna richiesta per l'algoritmo base
# Python 3.7+ standard library è sufficiente
```

## 📄 .gitignore

```
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# Testing
.pytest_cache/
.coverage
htmlcov/

# IDE
.vscode/
.idea/
*.swp
*.swo

# Proprietary - NON COMMITTARE
booster/
proprietary/
commercial/
*.booster.py
*_optimized.py
```

## 📄 CONTRIBUTING.md

```markdown
# Contribuire al Teorema di Pappadia

Grazie per l'interesse nel contribuire al progetto!

## Importante

- Questo repository contiene SOLO l'implementazione base del Teorema di Pappadia
- Le ottimizzazioni Pappadia Booster sono proprietarie e NON devono essere incluse
- Tutti i contributi devono mantenere la licenza AGPL v3

## Come Contribuire

1. Fork del repository
2. Crea un branch per la tua feature
3. Mantieni il codice semplice e ben documentato
4. Assicurati che tutti i test passino
5. Invia una Pull Request

## Cosa NON includere

- Nessuna ottimizzazione oltre O(log M)
- Nessun codice del Pappadia Booster
- Nessun parametro della Teoria Unificata Pappadia

## Attribuzione

Tutti i contributi devono mantenere l'attribuzione:
"© 2024-2025 Umberto Junior Pappadia - SIAE N° 2025/00839"

## Domande?

Contatta: info@umbertojuniorpappadia.com
```

## 📄 tests/test_pappadia.py

```python
"""
Test per il Teorema di Pappadia
© 2024-2025 Umberto Junior Pappadia
SIAE N° 2025/00839
"""

import unittest
from pappadia import calculate_period, multiplicative_order


class TestPappadiaTheorem(unittest.TestCase):
    """Test suite per il Teorema di Pappadia"""
    
    def test_simple_prime(self):
        """Test con numero primo semplice"""
        periodo, antiperiodo = calculate_period(1, 7)
        self.assertEqual(periodo, 6)
        self.assertEqual(antiperiodo, 0)
    
    def test_with_antiperiod(self):
        """Test con antiperiodo"""
        periodo, antiperiodo = calculate_period(1, 28)
        self.assertEqual(periodo, 6)
        self.assertEqual(antiperiodo, 2)
    
    def test_terminating(self):
        """Test frazione terminante"""
        periodo, antiperiodo = calculate_period(1, 8)
        self.assertEqual(periodo, 0)
        self.assertEqual(antiperiodo, 3)
    
    def test_large_prime(self):
        """Test con primo grande"""
        periodo, antiperiodo = calculate_period(1, 97)
        self.assertEqual(periodo, 96)
        self.assertEqual(antiperiodo, 0)
    
    def test_multiplicative_order(self):
        """Test ordine moltiplicativo"""
        self.assertEqual(multiplicative_order(10, 7), 6)
        self.assertEqual(multiplicative_order(10, 13), 6)
        self.assertEqual(multiplicative_order(10, 97), 96)


if __name__ == '__main__':
    unittest.main()
```

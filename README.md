#!/usr/bin/env python3
"""
Tiny SAT solver (DPLL) — educational prototype.
Run: python3 src/sat_solver_minisatish.py
"""
from typing import List, Dict, Optional
import random

Clause = List[int]  # integers: positive for var, negative for negated var
Formula = List[Clause]

def unit_propagate(formula: Formula, assignment: Dict[int, bool]):
    changed = True
    while changed:
        changed = False
        for clause in formula:
            unassigned = [lit for lit in clause if abs(lit) not in assignment]
            if len(unassigned) == 0:
                # clause must be satisfied
                if not any((lit > 0 and assignment.get(abs(lit))) or (lit < 0 and not assignment.get(abs(lit))) for lit in clause):
                    return False
            elif len(unassigned) == 1:
                lit = unassigned[0]
                val = lit > 0
                assignment[abs(lit)] = val
                changed = True
    return True

def dpll(formula: Formula, vars_list: List[int], assignment: Dict[int, bool]) -> Optional[Dict[int,bool]]:
    if not unit_propagate(formula, assignment):
        return None
    # check if all clauses satisfied
    all_sat = True
    for clause in formula:
        if not any((lit > 0 and assignment.get(abs(lit))) or (lit < 0 and not assignment.get(abs(lit))) for lit in clause):
            all_sat = False; break
    if all_sat:
        return assignment
    # pick unassigned var
    for v in vars_list:
        if v not in assignment:
            var = v; break
    else:
        return assignment
    for val in [True, False]:
        local = dict(assignment); local[var] = val
        res = dpll(formula, vars_list, local)
        if res is not None:
            return res
    return None

def demo():
    # example: (x1 or x2) and (not x1 or x3) and (not x2 or not x3)
    formula = [[1,2], [-1,3], [-2,-3]]
    vars_list = [1,2,3]
    sol = dpll(formula, vars_list, {})
    print("SAT solution:" , sol)

if __name__ == "__main__":
    demo()

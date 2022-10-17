The time it takes for a skill to load doesn't take into account the casting speed accumulated from items, potions, quests, etc.

How to fix it:

UserInterface/PythonSkill.cpp

Find:
```cpp
PyObject * skillGetSkillCoolTime(PyObject * poSelf, PyObject * poArgs)
```

Modify with:
```cpp
PyObject * skillGetSkillCoolTime(PyObject * poSelf, PyObject * poArgs)
{
	int iSkillIndex;
	if (!PyTuple_GetInteger(poArgs, 0, &iSkillIndex))
		return Py_BadArgument();

	float fSkillPoint;
	if (!PyTuple_GetFloat(poArgs, 1, &fSkillPoint))
		return Py_BadArgument();

	CPythonSkill::SSkillData * c_pSkillData;
	if (!CPythonSkill::Instance().GetSkillData(iSkillIndex, &c_pSkillData))
		return Py_BuildException("skill.GetSkillCoolTime - Failed to find skill by %d", iSkillIndex);

	//Skill cooldown fix
	int iCastingSpeed = CPythonPlayer::Instance().GetStatus(POINT_CASTING_SPEED);
	return Py_BuildValue("i", c_pSkillData->GetSkillCoolTime(fSkillPoint) * 100 / iCastingSpeed);
}
```
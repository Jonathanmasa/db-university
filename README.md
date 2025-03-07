# db-university

db-university-ER

<!-- SELECT -->
<!-- 1. Selezionare tutti gli studenti nati nel 1990 (160) -->

SELECT \*
FROM students
WHERE YEAR (date_of_birth) = 1990

<!-- 2. Selezionare tutti i corsi che valgono più di 10 crediti (479) -->

SELECT \*
FROM courses
WHERE cfu > 10;

<!-- 3. Selezionare tutti gli studenti che hanno più di 30 anni -->

SELECT \*
FROM students
WHERE YEAR(CURDATE()) - YEAR(date_of_birth) > 30;

<!-- 4. Selezionare tutti i corsi del primo semestre del primo anno di un qualsiasi corso di
laurea (286) -->

SELECT \*
FROM courses
WHERE year = 1
AND period = 'I semestre';

<!-- 5. Selezionare tutti gli appelli d'esame che avvengono nel pomeriggio (dopo le 14) del
20/06/2020 (21) -->

SELECT \*
FROM exams
WHERE date = '2020-06-20'
AND hour > '14:00:00';

<!-- 6. Selezionare tutti i corsi di laurea magistrale (38) -->

SELECT \*
FROM degrees
WHERE level = 'magistrale';

<!-- 7. Da quanti dipartimenti è composta l'università? (12) -->

SELECT COUNT(\*) AS num_dipartimenti
FROM departments;

<!-- 8. Quanti sono gli insegnanti che non hanno un numero di telefono? (50) -->

SELECT COUNT(\*) AS num_insegnanti_senza_telefono
FROM teachers
WHERE phone IS NULL;

<!-- GROUP -->
<!-- 1. Contare quanti iscritti ci sono stati ogni anno -->

SELECT YEAR(enrolment_date) AS anno, COUNT(\*) AS num_iscritti
FROM students
GROUP BY YEAR(enrolment_date)
ORDER BY anno;

<!-- 2. Contare gli insegnanti che hanno l'ufficio nello stesso edificio -->

SELECT office_address, COUNT(\*) AS num_insegnanti
FROM teachers
GROUP BY office_address;

<!-- 3. Calcolare la media dei voti di ogni appello d'esame -->

SELECT exam_id, AVG(vote) AS media_voti
FROM exam_student
GROUP BY exam_id;

<!-- 4. Contare quanti corsi di laurea ci sono per ogni dipartimento -->

SELECT d.department_id, dep.name AS department_name, COUNT(\*) AS num_degrees
FROM degrees d
JOIN departments dep ON d.department_id = dep.id
GROUP BY d.department_id, dep.name
ORDER BY num_degrees DESC;

<!-- QUERY CON JOIN -->
<!-- 1. Selezionare tutti gli studenti iscritti al Corso di Laurea in Economia -->

SELECT `students`.`name`,`students`.`surname`,`students`.`id`, `degrees`.`name` AS `name degrees`
FROM students
JOIN `degrees` ON `students`.`degree_id` = `degrees`.`id`  
WHERE `degrees`.`name`= 'Corso di Laurea in Economia';

<!-- 2. Selezionare tutti i Corsi di Laurea Magistrale del Dipartimento di
Neuroscienze -->

SELECT `degrees`.`name`, `departments`.`name`
FROM degrees
JOIN `departments` ON `degrees`.`department_id` = `departments`.`id`  
WHERE `departments`.`name`= 'Dipartimento di Neuroscienze';

<!-- 3. Selezionare tutti i corsi in cui insegna Fulvio Amato (id=44) -->

SELECT `teachers`.`name`, `teachers`.`surname`, `courses`.`name`
FROM courses
JOIN `course_teacher` ON `courses`.`id` = `course_teacher`.`course_id`
JOIN `teachers` ON `course_teacher`.`teacher_id` = `teachers`.`id`
WHERE `teachers`.`id`= 44;

<!-- 4. Selezionare tutti gli studenti con i dati relativi al corso di laurea a cui
sono iscritti e il relativo dipartimento, in ordine alfabetico per cognome e
nome -->

SELECT `students`.`id`, `students`.`name`, `students`.`surname`, `students`.`date_of_birth`, `students`.`fiscal_code`, `students`.`enrolment_date`, `degrees`.`name` AS `degree_name`, `degrees`.`level` AS `degree_level`, `departments`.`name` AS `department_name`
FROM students
JOIN `degrees` ON `students`.`degree_id` = `degrees`.`id`
JOIN `departments` ON `degrees`.`department_id` = `departments`.`id`
ORDER BY `students`.`surname`, `students`.`name`;

<!-- 5. Selezionare tutti i corsi di laurea con i relativi corsi e insegnanti -->

SELECT `degrees`._, `courses`._, `teachers`.\*
FROM degrees
JOIN `courses` ON `degrees`.`id` = `courses`.`degree_id`
LEFT JOIN `course_teacher` ON `courses`.`id` = `course_teacher`.`course_id`
LEFT JOIN `teachers` ON `course_teacher`.`teacher_id` = `teachers`.`id`
ORDER BY `degrees`.`name`, `courses`.`name`;

<!-- 6. Selezionare tutti i docenti che insegnano nel Dipartimento di
Matematica (54) -->

SELECT DISTINCT `teachers`.`id`, `teachers`.`name`,`teachers`.`surname`
FROM teachers
JOIN `course_teacher` ON `teachers`.`id` = `course_teacher`.`teacher_id`
JOIN `courses` ON `course_teacher`.`course_id` = `courses`.`id`
JOIN `degrees` ON `courses`.`degree_id` = `degrees`.`id`
JOIN `departments` ON `degrees`.`department_id` = `departments`.`id`
WHERE `departments`.`name` = 'Dipartimento di Matematica';

<!-- 7. BONUS: Selezionare per ogni studente il numero di tentativi sostenuti
per ogni esame, stampando anche il voto massimo. Successivamente,
filtrare i tentativi con voto minimo 18. -->
